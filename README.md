
# Auto Cropping Script (BasicAI, CSV)
## Description
This project provides a script to process JSON files containing image annotations downloaded from BasicAI (an image annotation platform) and crop images based on the bounding box (bbox) coordinates. The cropped images are then saved in a structured directory hierarchy based on their class names.

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [License](#license)

## Installation
Install [PyCharm Community Edition](https://www.jetbrains.com/pycharm/download/?section=windows)

## Usage
To use the script, you need to provide the path to the folder containing JSON files and images, and the output folder where the cropped images will be saved.

    1. Update the image_folder_path and output_folder_path variables in the script with the appropriate paths.
    2. Run the script:

### Example
Here is an example of how to use the script:

    import os
    import cv2
    import json


    def crop_bbox(image_path, bbox):
        try:
            image = cv2.imread(image_path)
            xmin, ymin, xmax, ymax = bbox
            cropped_image = image[ymin:ymax, xmin:xmax]
            return cropped_image
        except Exception as e:
            print(f"Error cropping image {image_path}: {str(e)}")
            return None


    def process_json_annotations(image_folder_path, output_folder_path):
        for filename in os.listdir(image_folder_path):
            if filename.endswith(".json"):
                json_file_path = os.path.join(image_folder_path, filename)
                with open(json_file_path, 'r') as json_file:
                    annotation = json.load(json_file)
                    image_filename = os.path.basename(json_file_path).replace('.json', '.jpg').replace('.jpg.jpg', '.jpg')
                    image_path = os.path.join(image_folder_path, image_filename)
                    if not os.path.exists(image_path):
                        print(f"Image not found: {image_path}")
                        continue
                    for obj in annotation['objects']:
                        class_name = obj['classTitle']
                        bbox = tuple(obj['points']['exterior'][0] + obj['points']['exterior'][1])
                        class_folder = os.path.join(output_folder_path, class_name)
                        if not os.path.exists(class_folder):
                            os.makedirs(class_folder)
                        cropped_image = crop_bbox(image_path, bbox)
                        if cropped_image is not None:
                            output_image_path = os.path.join(class_folder, image_filename)
                            if cv2.imwrite(output_image_path, cropped_image):
                                print(f"Image saved successfully: {output_image_path}")
                            else:
                                print(f"Error saving image: {output_image_path}")


    # Example usage
    image_folder_path = "/path/to/your/image/folder"
    output_folder_path = "/path/to/your/output/folder"
    process_json_annotations(image_folder_path, output_folder_path)


## License
This project is licensed under the [MIT License](https://www.mit.edu/~amini/LICENSE.md).



