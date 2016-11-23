**이 글은 Stanford CS231n Class의 글을 번역한 것 입니다.**


딥러닝의 Computer Vision 분야에 대해서 굉장히 좋은 양질의 강의를 제공하고 있는 [CS231n](http://cs231n.stanford.edu/syllabus.html) 에서 제공하는 강의입니다.
부족한 번역이지만.. 누군가에게는 이 글이 도움이 될 수도 있다고 생각이 되었고, 저 역시 번역을 하면서 글을 상세히 읽을 수 있는 기회가 된다고 생각이 되어서 앞으로의 노트들을 번역해서 올릴 생각입니다.
([원문보기 ](http://cs231n.github.io/classification/))

 
---


이 강의는 컴퓨터 비전을 잘 모르는 사람들을 위한 입문 강의로서 이미지 Classification 문제와 Data-driven 접근법에 대해 다룰 것 입니다. 목차는 다음과 같습니다.

 목차 :

- Intro to Image Classification, data-driven approach, pipeline
- Nearest Neighbor Classifier
	- k-Nearest Neighbor
- Validation sets, Cross-validation, hyperparameter tuning
- Pros/Cons of Nearest Neighbor
- Summary
- Summary: Applying kNN in practice
- Further Reading

## Image Classification


**Motivation**  이 섹션에서는 정해진 카테고리의 집합에 맞게 Input Image에 라벨을 정해주는, Image Classification problem 에 대해서 다룹니다. 이 문제는 간단하지만, 다양한 종류의 실용적인 Application들이 있는, Computer Vision에서 굉장히 중요한 문제 중 하나입니다. 더욱이, 뒤의 강의에서 다룰 Computer Vision의 다른 분야들은 Image classification로 정리될 수도 있습니다.

**Example** 예를들어, 아래 이미지를 보면 Image classification model가 하나의 이미지를 받아서 {cat, dog, hat, mug} 이 4가지 라벨의 확률을 할당합니다. 이 이미지에서 보이듯이, 컴퓨터에게 이미지는 큰 하나의 3차원 배열인 점을 기억해주세요. 이 예에서, 고양지는 가로 248 pixels, 세로 400 pixels 의 크기를 가지고 RGB(Red, Green, Blue) 즉, 3가지 색상의 채널을 가지고 있습니다. 그러므로 이미지는 248  X 400 X 3 의 숫자들 즉, 297,000 개의 숫자들로 구성되어 있습니다. 각각의 숫자들은 0(검은색) ~255 (하얀색) 까지의 범위를 가지는 정수형 숫자입니다. 우리의 일은 이 굉장히 큰 숫자를 하나의 라벨로 전환하는 것이죠.

---

![image](http://cs231n.github.io/assets/classify.png)


---

**Challenges**  이러한 시각적인 물체를 인식하는 일은 우리 사람에게는 상대적으로 쉬운 일인 것에 비해, Computer Vision의 관점에서 본다면 큰 가치있는 도전과제로 생각됩니다. 아래는 우리가 생각한 도전과제들의 리스트입니다(모든 도전과제는 아닙니다). 다시 한 번 이미지가 컴퓨터에게는 3차원 배열이라는 것을 기억해주시면서..:

- Viewpoint variation(시점에 따른 변화) : 하나의 물체도 여러 방면으로 보면 다 다르게 보이는 것
- Scale variation(크기에 따른 변화) : 물체들은 굉장히 다양한 크기로 보여집니다.(보이는 것뿐만 아니라, 실제세계가 그렇죠)
- Deformation(변형) : 많은 물체들은 여러가지 방법으로 변형이 됩니다
- Occlusion(교합) : 물체들은 겹쳐서 보일 수 있습니다. 그래서 때때로 물체의 작은 부분만이 보여질때도 있습니다
- Illumination conditions(밝기) : 조명, 밝기는 pixel level에 굉장히 큰 영향을 미칩니다..
- Background clutter(배경의 간섭) : 물체는 주변의 환경들과 함께 존재합니다. 이것이 이 물체를 인식하게 어렵게 만듭니다.
- Intra-class variation(내부종류의 다양화) : 해당 물체는 여러가지의 소분류들을 가지고 있습니다. 예를 들어 자동차의 경우, 바퀴, 창문, 손잡드 등의 여러가지 물체들을 포함합니다.

좋은 Image classification model 은 Intra-class variations에 민감함을 유지함과 동시에, 그외 다른 다양한 문제에 대해 한결같은 예상 결과를 유지해야 합니다.

---

![image](http://cs231n.github.io/assets/challenges.jpeg)

---

**Data-driven approach** 우리는 이미지를 어떻게 각 카테고리로 분류 할 수 있을까요? Sorting 알고리즘을 개발하는 것과는 다르게, 하나의 이미지를 고양이로 분류하는 알고리즘을 개발하는 것은 명확하지가 않습니다. 그러므로 코드에서 각각의 카레고리가 보이는 특징들을 세분화하기보다는, 우리는 어린 아이들에게 접근(어린 아이들이 배우는 방법) 하는 방법을 취하려고 합니다: 우리는 컴퓨터에게 각각의 class에 대한 많은 예제들과 예제 이미지들을 보고 각 class의 시각이미지에 대해서 배울 수 있는 학습 알고리즘을 개발해서 제공할 것 입니다. 이 접근법은 라벨이 달려있는 많은 training dataset에 의존하므로, data-driven approach라고 합니다. 아래 이미지들은 dataset의 예입니다.

---

![image](http://cs231n.github.io/assets/trainset.jpg)

 위 예제는 4가지 카테고리의 Training set들 입니다. 현실에서 우리는 수천가지의 카테고리와 각 카테고리에 할당되는 이미지들을 굉장히 많이 가질 것 입니다. 
 
 ---


**The image classification pipeline** 우리는 이미지 Classification을 하나의 이미지르 표현하는 pixel들의 배열을 가지고 하나의 Lable에 할당하는 것으로 보아왔습니다. 우리의 전체적인 Pipeline은 아래와 같이 정의할 수 있습니다:


- **Input:** Input은 여러가지 class들 중 하나의 label값을 가지는 N개의 이미지들로 구성되어 있습니다. 우리는 이 데이터들을 training set이라고 부릅니다.
- **Learning:** 우리는 training set을 사용해서 class들이 모두 어떤 데이터들을 가지는지 학습합니다. 우리는 이 단계를 classifier를 training 하는 것 혹은 learning a model이라고 부릅니다.
- **Evaluation:** 마지막으로, 우리는 classifier가 이전에는 본적이 없는 이미지들을 예측하는 결과들을 평가합니다. 다음으로 우리는 이 예측값들과 실제 라벨들을 비교해봅시다. 당연히 우리는 우리의 model이 실제 결과와 예측 값들이 많이 일치하기를 바라겠지요.( 이 실제값을 ground truth 라고 부릅니다.)

## Nearest Neighbor Classifier

 우리의 첫 번째 접근법으로서, Nearest Neighbor Classifier라고 부르는 방법을 개발할 것 입니다.
As our first approach, we will develop what we call a Nearest Neighbor Classifier. This classifier has nothing to do with Convolutional Neural Networks and it is very rarely used in practice, but it will allow us to get an idea about the basic approach to an image classification problem.

Example image classification dataset: CIFAR-10. One popular toy image classification dataset is the [CIFAR-10 dataset](http://www.cs.toronto.edu/~kriz/cifar.html). This dataset consists of 60,000 tiny images that are 32 pixels high and wide. Each image is labeled with one of 10 classes (for example "airplane, automobile, bird, etc"). These 60,000 images are partitioned into a training set of 50,000 images and a test set of 10,000 images. In the image below you can see 10 random example images from each one of the 10 classes:

---

![image](http://cs231n.github.io/assets/nn.jpg)

Left: Example images from the CIFAR-10 dataset. Right: first column shows a few test images and next to each we show the top 10 nearest neighbors in the training set according to pixel-wise difference.

---

Suppose now that we are given the CIFAR-10 training set of 50,000 images (5,000 images for every one of the labels), and we wish to label the remaining 10,000. The nearest neighbor classifier will take a test image, compare it to every single one of the training images, and predict the label of the closest training image. In the image above and on the right you can see an example result of such a procedure for 10 example test images. Notice that in only about 3 out of 10 examples an image of the same class is retrieved, while in the other 7 examples this is not the case. For example, in the 8th row the nearest training image to the horse head is a red car, presumably due to the strong black background. As a result, this image of a horse would in this case be mislabeled as a car.

You may have noticed that we left unspecified the details of exactly how we compare two images, which in this case are just two blocks of 32 x 32 x 3. One of the simplest possibilities is to compare the images pixel by pixel and add up all the differences. In other words, given two images and representing them as vectors I1,I2I1,I2 , a reasonable choice for comparing them might be the L1 distance:

d1(I1,I2)=∑p∣∣Ip1−Ip2∣∣
d1(I1,I2)=∑p|I1p−I2p|

Where the sum is taken over all pixels. Here is the procedure visualized:

---

![image](http://cs231n.github.io/assets/nneg.jpeg)

An example of using pixel-wise differences to compare two images with L1 distance (for one color channel in this example). Two images are subtracted elementwise and then all differences are added up to a single number. If two images are identical the result will be zero. But if the images are very different the result will be large.

---

Let's also look at how we might implement the classifier in code. First, let's load the CIFAR-10 data into memory as 4 arrays: the training data/labels and the test data/labels. In the code below, Xtr (of size 50,000 x 32 x 32 x 3) holds all the images in the training set, and a corresponding 1-dimensional array Ytr (of length 50,000) holds the training labels (from 0 to 9):


```python
Xtr, Ytr, Xte, Yte = load_CIFAR10('data/cifar10/') # a magic function we provide
# flatten out all images to be one-dimensional
Xtr_rows = Xtr.reshape(Xtr.shape[0], 32 * 32 * 3) # Xtr_rows becomes 50000 x 3072
Xte_rows = Xte.reshape(Xte.shape[0], 32 * 32 * 3) # Xte_rows becomes 10000 x 3072
```

Now that we have all images stretched out as rows, here is how we could train and evaluate a classifier:

```python

nn = NearestNeighbor() # create a Nearest Neighbor classifier class
nn.train(Xtr_rows, Ytr) # train the classifier on the training images and labels
Yte_predict = nn.predict(Xte_rows) # predict labels on the test images
# and now print the classification accuracy, which is the average number
# of examples that are correctly predicted (i.e. label matches)
print 'accuracy: %f' % ( np.mean(Yte_predict == Yte) )

```

Notice that as an evaluation criterion, it is common to use the accuracy, which measures the fraction of predictions that were correct. Notice that all classifiers we will build satisfy this one common API: they have a train(X,y) function that takes the data and the labels to learn from. Internally, the class should build some kind of model of the labels and how they can be predicted from the data. And then there is a predict(X) function, which takes new data and predicts the labels. Of course, we've left out the meat of things - the actual classifier itself. Here is an implementation of a simple Nearest Neighbor classifier with the L1 distance that satisfies this template:

```python
import numpy as np

class NearestNeighbor(object):
  def __init__(self):
    pass

  def train(self, X, y):
    """ X is N x D where each row is an example. Y is 1-dimension of size N """
    # the nearest neighbor classifier simply remembers all the training data
    self.Xtr = X
    self.ytr = y

  def predict(self, X):
    """ X is N x D where each row is an example we wish to predict label for """
    num_test = X.shape[0]
    # lets make sure that the output type matches the input type
    Ypred = np.zeros(num_test, dtype = self.ytr.dtype)

    # loop over all test rows
    for i in xrange(num_test):
      # find the nearest training image to the i'th test image
      # using the L1 distance (sum of absolute value differences)
      distances = np.sum(np.abs(self.Xtr - X[i,:]), axis = 1)
      min_index = np.argmin(distances) # get the index with smallest distance
      Ypred[i] = self.ytr[min_index] # predict the label of the nearest example

    return Ypred 
```

If you ran this code, you would see that this classifier only achieves 38.6% on CIFAR-10. That's more impressive than guessing at random (which would give 10% accuracy since there are 10 classes), but nowhere near human performance (which is estimated at about 94%) or near state-of-the-art Convolutional Neural Networks that achieve about 95%, matching human accuracy (see the leaderboard of a recent Kaggle competition on CIFAR-10).

The choice of distance. There are many other ways of computing distances between vectors. Another common choice could be to instead use the L2 distance, which has the geometric interpretation of computing the euclidean distance between two vectors. The distance takes the form:

d2(I1,I2)=∑p(Ip1−Ip2)2‾‾‾‾‾‾‾‾‾‾‾‾‾√
d2(I1,I2)=∑p(I1p−I2p)2
In other words we would be computing the pixelwise difference as before, but this time we square all of them, add them up and finally take the square root. In numpy, using the code from above we would need to only replace a single line of code. The line that computes the distances:

```python
distances = np.sqrt(np.sum(np.square(self.Xtr - X[i,:]), axis = 1))
```

Note that I included the np.sqrt call above, but in a practical nearest neighbor application we could leave out the square root operation because square root is a monotonic function. That is, it scales the absolute sizes of the distances but it preserves the ordering, so the nearest neighbors with or without it are identical. If you ran the Nearest Neighbor classifier on CIFAR-10 with this distance, you would obtain 35.4% accuracy (slightly lower than our L1 distance result).

L1 vs. L2. It is interesting to consider differences between the two metrics. In particular, the L2 distance is much more unforgiving than the L1 distance when it comes to differences between two vectors. That is, the L2 distance prefers many medium disagreements to one big one. L1 and L2 distances (or equivalently the L1/L2 norms of the differences between a pair of images) are the most commonly used special cases of a p-norm.


## k - Nearest Neighbor Classifier

You may have noticed that it is strange to only use the label of the nearest image when we wish to make a prediction. Indeed, it is almost always the case that one can do better by using what's called a k-Nearest Neighbor Classifier. The idea is very simple: instead of finding the single closest image in the training set, we will find the top k closest images, and have them vote on the label of the test image. In particular, when k = 1, we recover the Nearest Neighbor classifier. Intuitively, higher values of k have a smoothing effect that makes the classifier more resistant to outliers:

---

![image](http://cs231n.github.io/assets/knn.jpeg)


An example of the difference between Nearest Neighbor and a 5-Nearest Neighbor classifier, using 2-dimensional points and 3 classes (red, blue, green). The colored regions show the decision boundaries induced by the classifier with an L2 distance. The white regions show points that are ambiguously classified (i.e. class votes are tied for at least two classes). Notice that in the case of a NN classifier, outlier datapoints (e.g. green point in the middle of a cloud of blue points) create small islands of likely incorrect predictions, while the 5-NN classifier smooths over these irregularities, likely leading to better generalization on the test data (not shown). Also note that the gray regions in the 5-NN image are caused by ties in the votes among the nearest neighbors (e.g. 2 neighbors are red, next two neighbors are blue, last neighbor is green).

---

In practice, you will almost always want to use k-Nearest Neighbor. But what value of k should you use? We turn to this problem next.

## Validation sets for Hyperparameter tuning

The k-nearest neighbor classifier requires a setting for k. But what number works best? Additionally, we saw that there are many different distance functions we could have used: L1 norm, L2 norm, there are many other choices we didn't even consider (e.g. dot products). These choices are called hyperparameters and they come up very often in the design of many Machine Learning algorithms that learn from data. It's often not obvious what values/settings one should choose.

You might be tempted to suggest that we should try out many different values and see what works best. That is a fine idea and that's indeed what we will do, but this must be done very carefully. In particular, we cannot use the test set for the purpose of tweaking hyperparameters. Whenever you're designing Machine Learning algorithms, you should think of the test set as a very precious resource that should ideally never be touched until one time at the very end. Otherwise, the very real danger is that you may tune your hyperparameters to work well on the test set, but if you were to deploy your model you could see a significantly reduced performance. In practice, we would say that you overfit to the test set. Another way of looking at it is that if you tune your hyperparameters on the test set, you are effectively using the test set as the training set, and therefore the performance you achieve on it will be too optimistic with respect to what you might actually observe when you deploy your model. But if you only use the test set once at end, it remains a good proxy for measuring the generalization of your classifier (we will see much more discussion surrounding generalization later in the class).

> Evaluate on the test set only a single time, at the very end.

Luckily, there is a correct way of tuning the hyperparameters and it does not touch the test set at all. The idea is to split our training set in two: a slightly smaller training set, and what we call a validation set. Using CIFAR-10 as an example, we could for example use 49,000 of the training images for training, and leave 1,000 aside for validation. This validation set is essentially used as a fake test set to tune the hyper-parameters.

Here is what this might look like in the case of CIFAR-10:

```python
# assume we have Xtr_rows, Ytr, Xte_rows, Yte as before
# recall Xtr_rows is 50,000 x 3072 matrix
Xval_rows = Xtr_rows[:1000, :] # take first 1000 for validation
Yval = Ytr[:1000]
Xtr_rows = Xtr_rows[1000:, :] # keep last 49,000 for train
Ytr = Ytr[1000:]

# find hyperparameters that work best on the validation set
validation_accuracies = []
for k in [1, 3, 5, 10, 20, 50, 100]:

  # use a particular value of k and evaluation on validation data
  nn = NearestNeighbor()
  nn.train(Xtr_rows, Ytr)
  # here we assume a modified NearestNeighbor class that can take a k as input
  Yval_predict = nn.predict(Xval_rows, k = k)
  acc = np.mean(Yval_predict == Yval)
  print 'accuracy: %f' % (acc,)

  # keep track of what works on the validation set
  validation_accuracies.append((k, acc))
```

By the end of this procedure, we could plot a graph that shows which values of k work best. We would then stick with this value and evaluate once on the actual test set.

> Split your training set into training set and a validation set. Use validation set to tune all hyperparameters. At the end run a single time on the test set and report performance.

Cross-validation. In cases where the size of your training data (and therefore also the validation data) might be small, people sometimes use a more sophisticated technique for hyperparameter tuning called cross-validation. Working with our previous example, the idea is that instead of arbitrarily picking the first 1000 datapoints to be the validation set and rest training set, you can get a better and less noisy estimate of how well a certain value of k works by iterating over different validation sets and averaging the performance across these. For example, in 5-fold cross-validation, we would split the training data into 5 equal folds, use 4 of them for training, and 1 for validation. We would then iterate over which fold is the validation fold, evaluate the performance, and finally average the performance across the different folds.

---

![image](http://cs231n.github.io/assets/cvplot.png)


Example of a 5-fold cross-validation run for the parameter k. For each value of k we train on 4 folds and evaluate on the 5th. Hence, for each k we receive 5 accuracies on the validation fold (accuracy is the y-axis, each result is a point). The trend line is drawn through the average of the results for each k and the error bars indicate the standard deviation. Note that in this particular case, the cross-validation suggests that a value of about k = 7 works best on this particular dataset (corresponding to the peak in the plot). If we used more than 5 folds, we might expect to see a smoother (i.e. less noisy) curve.

---


In practice. In practice, people prefer to avoid cross-validation in favor of having a single validation split, since cross-validation can be computationally expensive. The splits people tend to use is between 50%-90% of the training data for training and rest for validation. However, this depends on multiple factors: For example if the number of hyperparameters is large you may prefer to use bigger validation splits. If the number of examples in the validation set is small (perhaps only a few hundred or so), it is safer to use cross-validation. Typical number of folds you can see in practice would be 3-fold, 5-fold or 10-fold cross-validation.

---

![image](http://cs231n.github.io/assets/crossval.jpeg)


Common data splits. A training and test set is given. The training set is split into folds (for example 5 folds here). The folds 1-4 become the training set. One fold (e.g. fold 5 here in yellow) is denoted as the Validation fold and is used to tune the hyperparameters. Cross-validation goes a step further iterates over the choice of which fold is the validation fold, separately from 1-5. This would be referred to as 5-fold cross-validation. In the very end once the model is trained and all the best hyperparameters were determined, the model is evaluated a single time on the test data (red).

---

#### Pros and Cons of Nearest Neighbor classifier.

It is worth considering some advantages and drawbacks of the Nearest Neighbor classifier. Clearly, one advantage is that it is very simple to implement and understand. Additionally, the classifier takes no time to train, since all that is required is to store and possibly index the training data. However, we pay that computational cost at test time, since classifying a test example requires a comparison to every single training example. This is backwards, since in practice we often care about the test time efficiency much more than the efficiency at training time. In fact, the deep neural networks we will develop later in this class shift this tradeoff to the other extreme: They are very expensive to train, but once the training is finished it is very cheap to classify a new test example. This mode of operation is much more desirable in practice.

As an aside, the computational complexity of the Nearest Neighbor classifier is an active area of research, and several Approximate Nearest Neighbor (ANN) algorithms and libraries exist that can accelerate the nearest neighbor lookup in a dataset (e.g. FLANN). These algorithms allow one to trade off the correctness of the nearest neighbor retrieval with its space/time complexity during retrieval, and usually rely on a pre-processing/indexing stage that involves building a kdtree, or running the k-means algorithm.

The Nearest Neighbor Classifier may sometimes be a good choice in some settings (especially if the data is low-dimensional), but it is rarely appropriate for use in practical image classification settings. One problem is that images are high-dimensional objects (i.e. they often contain many pixels), and distances over high-dimensional spaces can be very counter-intuitive. The image below illustrates the point that the pixel-based L2 similarities we developed above are very different from perceptual similarities:

---

![image](http://cs231n.github.io/assets/samenorm.png)

Pixel-based distances on high-dimensional data (and images especially) can be very unintuitive. An original image (left) and three other images next to it that are all equally far away from it based on L2 pixel distance. Clearly, the pixel-wise distance does not correspond at all to perceptual or semantic similarity.

---

Here is one more visualization to convince you that using pixel differences to compare images is inadequate. We can use a visualization technique called t-SNE to take the CIFAR-10 images and embed them in two dimensions so that their (local) pairwise distances are best preserved. In this visualization, images that are shown nearby are considered to be very near according to the L2 pixelwise distance we developed above:

---

![image](http://cs231n.github.io/assets/pixels_embed_cifar10.jpg)


CIFAR-10 images embedded in two dimensions with t-SNE. Images that are nearby on this image are considered to be close based on the L2 pixel distance. Notice the strong effect of background rather than semantic class differences. Click here for a bigger version of this visualization.

---

In particular, note that images that are nearby each other are much more a function of the general color distribution of the images, or the type of background rather than their semantic identity. For example, a dog can be seen very near a frog since both happen to be on white background. Ideally we would like images of all of the 10 classes to form their own clusters, so that images of the same class are nearby to each other regardless of irrelevant characteristics and variations (such as the background). However, to get this property we will have to go beyond raw pixels.


## Summary

In summary:

- We introduced the problem of Image Classification, in which we are given a set of images that are all labeled with a single category. We are then asked to predict these categories for a novel set of test images and measure the accuracy of the predictions.
- We introduced a simple classifier called the Nearest Neighbor classifier. We saw that there are multiple hyper-parameters (such as value of k, or the type of distance used to compare examples) that are associated with this classifier and that there was no obvious way of choosing them.
- We saw that the correct way to set these hyperparameters is to split your training data into two: a training set and a fake test set, which we call validation set. We try different hyperparameter values and keep the values that lead to the best performance on the validation set.
- If the lack of training data is a concern, we discussed a procedure called cross-validation, which can help reduce noise in estimating which hyperparameters work best.
- Once the best hyperparameters are found, we fix them and perform a single evaluation on the actual test set.
- We saw that Nearest Neighbor can get us about 40% accuracy on CIFAR-10. It is simple to implement but requires us to store the entire training set and it is expensive to evaluate on a test image.
- Finally, we saw that the use of L1 or L2 distances on raw pixel values is not adequate since the distances correlate more strongly with backgrounds and color distributions of images than with their semantic content.

In next lectures we will embark on addressing these challenges and eventually arrive at solutions that give 90% accuracies, allow us to completely discard the training set once learning is complete, and they will allow us to evaluate a test image in less than a millisecond.


## Summary: Applying kNN in practice

If you wish to apply kNN in practice (hopefully not on images, or perhaps as only a baseline) proceed as follows:

1. Preprocess your data: Normalize the features in your data (e.g. one pixel in images) to have zero mean and unit variance. We will cover this in more detail in later sections, and chose not to cover data normalization in this section because pixels in images are usually homogeneous and do not exhibit widely different distributions, alleviating the need for data normalization.
2. If your data is very high-dimensional, consider using a dimensionality reduction technique such as PCA (wiki ref, CS229ref, blog ref) or even Random Projections.
3. Split your training data randomly into train/val splits. As a rule of thumb, between 70-90% of your data usually goes to the train split. This setting depends on how many hyperparameters you have and how much of an influence you expect them to have. If there are many hyperparameters to estimate, you should err on the side of having larger validation set to estimate them effectively. If you are concerned about the size of your validation data, it is best to split the training data into folds and perform cross-validation. If you can afford the computational budget it is always safer to go with cross-validation (the more folds the better, but more expensive).
4. Train and evaluate the kNN classifier on the validation data (for all folds, if doing cross-validation) for many choices of k (e.g. the more the better) and across different distance types (L1 and L2 are good candidates)
5. If your kNN classifier is running too long, consider using an Approximate Nearest Neighbor library (e.g. FLANN) to accelerate the retrieval (at cost of some accuracy).
6. Take note of the hyperparameters that gave the best results. There is a question of whether you should use the full training set with the best hyperparameters, since the optimal hyperparameters might change if you were to fold the validation data into your training set (since the size of the data would be larger). In practice it is cleaner to not use the validation data in the final classifier and consider it to be burned on estimating the hyperparameters. Evaluate the best model on the test set. Report the test set accuracy and declare the result to be the performance of the kNN classifier on your data.

### Further Reading

Here are some (optional) links you may find interesting for further reading:

- A Few Useful Things to Know about Machine Learning, where especially section 6 is related but the whole paper is a warmly recommended reading.
- Recognizing and Learning Object Categories, a short course of object categorization at ICCV 2005.