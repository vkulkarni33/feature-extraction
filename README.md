This script performs Optical Character Recognition (OCR) on images to extract specific information, such as weight, voltage, or other units, using a combination of OCR models and a text generation model.

Step 1: Install Necessary Libraries
        Before running the script, ensure you have installed the required dependencies such as pandas, Pillow, OpenCV, and the surya OCR package. You also need the ollama model for generating 
        text-based responses.
        
Step 2: Import Libraries and Models
        The script begins by importing essential libraries:
        pandas: For reading and manipulating CSV files.
        PIL.Image: For handling images.
        surya.ocr and related models: Used for detecting and recognizing text from images.
        ollama: A text generation model to provide structured responses.
        cv2 (OpenCV): For reading and processing images.
        
Step 3: Define the predictor Function
        The predictor function processes each image, runs OCR to extract text, and uses a model to generate a structured response. 
        Here’s how it works:
       1. Download Image: Downloads the image from the given URL using urllib.request
                   urllib.request.urlretrieve(image_link, "image.jpg")
       2. Process Image: Converts the image into a format that can be used by the OCR models. The image is read using OpenCV and then converted into a PIL.Image.
                   image_bgr = cv2.imread("image.jpg")
                   image = Image.fromarray(image_bgr)
       3. Load OCR Models:Loads both the detection and recognition models needed for the OCR process.
                   det_processor, det_model = load_det_processor(), load_det_model()
                   rec_model, rec_processor = load_rec_model(), load_rec_processor()
       4. Perform OCR: Extracts text from the image. The result is a list of text lines that the model detects.
                   predictions = run_ocr([image], [langs], det_model, det_processor, rec_model, rec_processor)
       5.Extract Text:Combines the extracted text into a single string.
                   extracted_text = ' '.join([text_line.text for result in predictions for text_line in result.text_lines])
       6.Generate a Response: Uses the extracted text to ask the ollama model a specific question (e.g., "What is the weight of the product?"). The response must follow strict rules about 
                            number and unit formats (e.g., "123 gram").
                   response = generate('llama3.1:8b', f"{extracted_text} What is the {entity_name} of the product?")
       7.Validate Response: Ensures that the model’s response is valid. If the response is invalid (no number found or wrong format), the function returns an empty string.
                   if response == 'None' or len(response.split()) > 3:
                     return ''
                   return response
 Step 4: Apply the predictor Function on the Dataset.
         In the main part of the script, the predictor function is applied to a dataset of images, and the predictions are saved in a CSV file.
        1. Read CSV File: Reads the input dataset (a CSV file) containing image links, categories, and entity names.
                           test = pd.read_csv('sample-test.csv')
        2. Apply the Predictor: Uses the predictor function to process each image in the dataset and generate predictions.
                           test['prediction'] = test.apply(lambda row: predictor(row['index'], row['image_link'], row['group_id'], row['entity_name']), axis=1)
        3. Save Results: The predictions are saved to an output CSV file.
                           output_filename = 'test_out-1.csv'
                           test[['index', 'prediction']].to_csv(output_filename, index=False)
  Step 5: Run the Script.
         To run the script, ensure you have a CSV file with the following columns:

        index: A unique identifier for each row.
        image_link: The URL of the image to process.
        category_id: The category of the entity to be extracted (e.g., weight, voltage).
        entity_name: The specific entity to be extracted (e.g., "weight", "voltage").

  Note: Performance Consideration for Large Datasets
          If you are processing a large dataset, especially with high-resolution images, the process may be slow or require significant computational resources. In such cases,  the script on 
          a system with a powerful GPU to speed up the OCR and text generation tasks. The OCR models and text generation models are computationally expensive, 
          so a better GPU can significantly reduce the processing time.





                   




