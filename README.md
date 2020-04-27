# Pause (Flask based schedule image analysis with android mobile app client)

*[Version française](https://github.com/Mircea-Gosman/pause_v1/blob/master/README_FR.md)*

End of Cegep integration project. 

## Image analysis process
Since the starting image is meant to be a screenshot from the user's screen, custom image segmentation is mandatory to obtain decent acceptable OCR results. 

Such segmentation requires the identification of certain features in the image, namely:
* Contours
* Corners
* Individual line segments 

### Contours
The first step in the image segmentation procedure is to identify the schedule area. As OpenCV's [contours](https://docs.opencv.org/3.4/d4/d73/tutorial_py_contours_begin.html) allows for polygon identification, the task becomes to select the largest polygon as the schedule's bounding box. Adding padding to the cropped image helps reduce errors in further steps. 

| Original Image  | Cropped Image |
| ------------- | ------------- |
| <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/schedule.jpeg" width="300">  | <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/cropped.jpg" width="300">  |

### Corners
With the cropped schedule in hand, it is possible to identify all the corners in the image as coordinates using OpenCV's [Harris Corner algorithm](https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_feature2d/py_features_harris/py_features_harris.html). Once captured, the corners are filtered into respective columns and normalized across all columns. 

| Raw Corners  | Filtered Corners |
| ------------- | ------------- |
| <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/allCorners.jpg" width="300">  | <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/dayBoundingCorners.jpg" width="300">  |

### Line Segments
As the corners correspond to most of the intersections of the schedule's grid's outlines, they do not necessarily correspond to the courses corners. In order to filter out the extra corners, a check of quantity of horizontal line segments between two corners must be performed. OpenCV's [HoughLinesP algorithm](https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_houghlines/py_houghlines.html) is used in order to extract the lines segments from the image.

| Raw Line Segments  | Horizontal Line Segments Corners | Connected Corners |
| ------------- | ------------- | ------------- |
| <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/allLineSegments.jpg" width="300">  | <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/horizontalLineSegments.jpg" width="300">  | <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/pairedCorners.jpg" width="300">  |

### Grid Boxes
Pairs of corners can be paired two by two in order to form boxes. 

| Raw Corners  |
| ------------- | 
| <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/scheduleCells.jpg" width="300">  |

This correctly outlines the schedule's features and allows for cropping of every cell in preparation of the OCR process. 
(White padding is added to every cropped cell. Cell images are also upscaled by factor of two.)

| Time Cell | Course Cell |
| ------------- | ------------- |
| <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/hourCell.jpg" width="100">  | <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/courseCell.jpg" width="100">  |

### OCR
The OCR engine used is [Tesseract](https://tesseract-ocr.github.io), the [py_tesseract](https://pypi.org/project/pytesseract/) adaptation.

### End result 
The previously documented process allows both for decent OCR results and accurate positionning of the schedule's cells. 
Placed in a, SQL-Lite database, the ensued matching of courses to their start and end times appears as follows:

| Days' Table | Courses' Table |
| ------------- | ------------- |
| <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/daysDB.png" width="500">  | <img src="https://github.com/Mircea-Gosman/pause_v1/blob/master/Results/coursesDB.png" width="500">  |

## Server & Database
The server is based on Flask and the database code uses [Flask_Sql_Alchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/) to communicate with the server. 

As of now, there is support for:
* Registering users 
* Registering friend connections
* Importing a schedule to the Database from the client
* Updating a schedule in the Database from the client

As of now, there is no support for:
* Live Facebook friends updates (if the friend connection is made after user registration to the app) ([webhooks](https://developers.facebook.com/docs/graph-api/webhooks/))
* Between user notification transmission
* Deleting an user
* Logging out (relevant if option is implemented in client app, i.e. FB Messenger doesn't)

## Client
As of now, the client has:
* A log-in page 
* A home page
* A profile page

Basic navigation is implemented. Many/most UI features are not present. 
The client supports all the server's actual features. 

Its model needs to be able to compare schedules. Once that part is done, the rest of the app is pretty straight forward.




