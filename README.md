# Computer vision: Traffic sign detection and classification
This repository contains our attempt to detect and classify traffic signs in images from the German Traffic Sign dataset. Coauthors: [@jomartla](https://github.com/jomartla) &amp; [@MateuszKlimas](https://github.com/MateuszKlimas)

[Here](/Report%20Computer%20Vision.pdf) you can read the full report of our work. Below, a short summary of the steps taken:


### Problem statement
The problem we aimed to solve can be stated with the following sentence:
> Given an image, find all the traffic signs within it (detection) and tell which ones they are (classification).

### Our approach
To solve this problem we designed and trained two CNN networks:
* **Binary classifier**: a network which decides if an image has a traffic sign within it or not. For the non-traffic-sign class in our dataset we randomly selected segments from the background of the images (with cars, buildings, trees, etc). 

* **Categorical classifier**: a network that, given an image of a traffic sign, decides which type of sign it is. Since the data was scarce, we had to use **data augmentation** techiniques to help the network generalize better. We expanded our dataset by rotating slightly or images and changing their brightness
[!data_augmentation]()

#### Pipeline
Our final pipeline follows these steps :
- We use Faster-RCNN, a Resnet proposed in [this paper](https://arxiv.org/abs/1506.01497) to predict potential bounding boxes (bboxes) which might contain traffic signs. Each bbox is defined by the coordinates of 2 pixels (top left and bottom right) saying where the traffic sign might be in the original image. 
- We filter the resnet´s predictions:
  - Discarding bboxes on the **top or bottom 100 px** of the image (no signs in the sky or the asphalt)
  - Discarding bboxes whose **ratio** is not 3:4 < ratio < 4:3
  - Discarding bboxes whose **size > 75x75 px**
  - Discarding bboxes whose pixels where **white > 85 % **
Here you have an example of the resnet´s remaining bboxes after passing this filters:
[!resnet_bboxes]()
- We pass the pixels of the bboxes which are left to the Binary classifier, which discards the ones which contain background.
- We pass the rest to the Categorical classifier, which determines which kind of traffic sign each bbox contains.
- Lastly we pass all the detected traffic signs which belong to the same image through a **non-maximum-suppression** model. This checks if two or more predictions overlap and are incoherent, and selects the one with most probability of being correct.
[!non_max_sup]()


## Results:
We run a grid search for some hyperparameters in this pipeline and obtained an overall accuracy of 25.35%.
Here an example of correct traffic sign detection and classification
[!correct_pred]()

We realized that our model missclassified red cars and other objects as traffic signs, which dropped the accuracy drastically.
See the example below:
[!incorrect_pred]()



