# Large-scale Collection & Aggregation (LSCA) Paradigm 

## Data 
This approach requires image data and corresponding metadata in a standardized format. The bare minimum features are (1) date/time of capture and (2) capture location. As a given, the granularity of downstream results is increased as (1) and (2) increase. An optional, yet useful field of metadata is camera heading; this allows for geometric processing for only the visible portion of a street, as 'seen' by the camera. 

### Sample Data: 2020 Cornell-Nexar NYC Street Scenes 
From prior work, we provide a 2.4-million image dataset of New York City (NYC) street scenes, generated by Nexar. Nexar is a cloud-connected dashcam manufacturer, allowing them to tap into a large moat of connected cameras. 

These images are 1280x720 (720p) resolution each, and include (1) to the second granularity and (2) at a granularity of raw latitude/longitude. 

The dataset is accessible [here](https://LINK). Request access through Dataverse and mention you are trying to replicate this paper, and we will provide you with a downloader role. 

## Processing 
We provide code to do the following: 
1. Detect pedestrians, vehicles, and other objects of interest in LSCA-compliant images. 
2. Load a road graph of the area of interest. 
3. Merge and aggregate counts of detected objects with the road graph, producing road-by-road object (or, traffic) densities. 

We will describe each step in detail.

### 1. Object Detection 
Our object detection pipeline is a distributed wrapper around YOLOv7, using the still-state-of-the-art E6E weights. The code is housed in *detector.py*. There are certain considerations to keep in mind when using this script: 
- For small quantities of images, it may be easier to run inference using the stock inferencer.py script in the [YOLOv7](https://LINK) repo. Our script assumes between 10^6-10^7 images to be processed, and the distribution overhead may not be time-effective on smaller quantities. 
- The distribution pipeline is tuned for the RTX A6000 GPU, with 48GB of VRAM. Different configurations are untested, but you can use a generally-linear rule of thumb when changing the NUM_TASKS_PER_GPU variable.

When this script finishes processing your input images, you should a nested output directory that matches the layout of your input directory, with a text (.txt) file for each image. In this file, there will be a line of text for each detected object, in standard YOLO format (COCO object class,center x coordinate, center y coordinate, width of region, height of region, and confidence score).

### 1a. Aggregating Detections 
It is useful to consolidate the per-image .txt files into an easier-to-process format. We provide this functionality in [parse_detections.py](LINK). Example usage: 

### 2. Loading a Road Graph 
