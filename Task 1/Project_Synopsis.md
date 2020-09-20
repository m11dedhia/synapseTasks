# Object Detection

#### Detect classes of objects from a given picture stream in real time.

### Options Available
Object Detection can be done using *R-CNN’s (Regions with Convolutional Neural Networks)* as well as using the *YOLO algorithm (You only look once)*. I have selected the YOLO algorithm since it is much more efficient and as the name suggests only looks at the image once even though the number of trainable parameters increases. This algorithm enables you to detect and outline multiple as well as overlapping objects inside an image.

### The Basic Idea
The algorithm pre-processes an image let’s say the size of the images from the feed is *1280 x 720 (720p)*. And our target size of the image that we will input in the yolo algorithm is 608 x 608 for ease of computation explained further ahead. Since we take a coloured image, we will be having three channels that is RGB so the input dimensions of the image are (608, 608, 3). Now coming onto the part as to why we take 608 x 608, this is because in our model we divide our whole image into a grid of *19 x 19* in which we will be doing our computations.
We use a deep neural network on our *(608, 608, 3)* image and output a *(19, 19, Nanchors , Nclasses )* where Nanchors  is the number of anchor boxes defined by us and Nclasses is the number of classes + the confidence score of each anchor box for each cell and the dimensions of the box that it has detected. Typically, it contains *(bx , by , bh , bw )* (I’ll explain face to face). The confidence score is the probability of an object being present in the cell. It is multiplied by each class score (that indicates the probability of the class being there in that box) to get a box_class_confidence. We take the max out of this and assign it to the corresponding box, along with the deduced dimensions of the box that is to formed. We do this for each box and get approximately 2000 box predictions with varying confidence scores. We set a threshold for the confidence scores. All boxes having confidence scores under the threshold are eliminated and aren’t taken into further consideration. Now we get a reduced number of boxes but when we visualize the boxes, we get to know that the model has predicted several boxes for one particular class. We overcome this problem by using Non-Max Suppression. It is an algorithm that outputs one single box localization for each class that is actually present in the image. It uses the IOU algorithm that expands into Intersection over Union. Lets say we have several different boxes with different confidence scores (all above the threshold level) since they have been detected by two different boxes, they might be having different box attributes. To determine which one is to be kept and which one is to be discarded, we calculate the intersection as well as union of those two boxes. By creating a ratio of Intersection over Union we get a number between 0 and 1 indicating how much overlap these two images have. If they fall above a certain IOU threshold that has been predefined, they are kept otherwise the box with the lower confidence score is discarded. This way we come at a conclusion with a considerably smaller number of boxes defined by the algorithm which are used for cost calculation to initiate the backpropagation step. When coming on to the activation functions for the model, we use leaky RELU function with a factor of 0.1x if the input for the function x < 0. For the output layer we use the linear activation function.

### The Cost Calculation
The cost calculation is pretty complex and different from normal binary cross entropy cost function which makes use of the fact that when we have to predict a yes or no answer i.e. having two objects/or determining if the output is the required or no. Since the output of this model is a multiclass one, we use a slightly different cost function. The algorithm uses sum-squared error since it is easy to optimize but it also brings with it the problem of not being able to **maximize maximum average precision (maP)**. It also penalizes boxes that do not have any object heavily, the sole reason being they did not have or essentially detect an object when there was no object to detect. This could lead to the model diverging from the required results early on in the training period. It is avoided by incorporating the model increases the loss from bounding box predictions and decrease the loss from the confidence score for the boxes that did not contain any objects. We do this by introducing two parameters.

### Avoiding common problems
To avoid problems like overfitting, methods like dropout are used where randomly a predefined number of neurons are dropped for that pass of forward propagation in a particular layer of the neural network. Another method that is used is data augmentation like the 1 to 10 method where one image is used to produce 10 different images using only cropping. We can even use tools like colour shifting (limit of upto 1.5 in HSV colour space)  and horizontal sheer on the image. Random scaling and translations upto 20% of the original image size have been taken.



### What this model hasn’t overcome and does it have any scope of improvement?
Yolo only allows certain number of predictable boxes which have only one class (we use max function to determine which one) , this model struggles to identify small objects that appear repeatedly in a small area in the image like a swan on closely spaced ducks.
Since our model uses anchor boxes, it limits itself from identifying objects of unusual aspect ratios. Also since we down sample the image using several layers of convolutions, we introduce the concept of t using coarse features for determining the bounding boxes.


*I will be explaining how a basic convolution layer works and how we max-pooling after that to reduce the dimensions of the block.
*You can ask me questions down to the basics such as what are neurons, what do they implicate, why do we use neural networks and make a small L-layered neural network. I know the math behind these things too

#### Megh Dedhia

#### 60004190067

#### SE Computer Engineering - A