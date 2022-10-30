+++
title = "Logistic regression algorithm"
date = 2021-03-01 16:42:14
slug = "202103011642"

[taxonomies]
tags = ["R", "Python", "Machine Learning" ]
categories = ["Data Science"]

+++

<!-- more -->

# Logistic regression algorithm

​	Logistic regression is a supervised learning classification algorithm used to predict the probability of a target variable. The nature of target or dependent variable is dichotomous, which means there would be only two possible classes.

​	In simple words, the dependent variable is binary in nature having data coded as either 1 (stands for success/yes) or 0 (stands for failure/no).



In my opinion，

Logistic regression = Linear regression + Sigmoid function

Linear regression: 	z = w*x+b

Sigmoid function: 	<img src=".\img\sigmoid.PNG" style="zoom: 33%;" />

So, logistic regression:	<img src=".\img\lr.jpg" alt="lr" style="zoom:15%;" />



## Example of Logistic Regression in Python



```python
# Import library
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
# Loading datasets
cancer = datasets.load_breast_cancer()
cancer_X = cancer.data
print(cancer_X.shape)
cancer_y = cancer.target
print(cancer_y.shape)
# Dividing the training set and test set
X_train, X_test, y_train, y_test = train_test_split(
    cancer_X, cancer_y, test_size=0.3)
# Model construction and training
clf = LogisticRegression(max_iter=10000)  # max_iter is needed
clf.fit(X_train, y_train)
# Model Testing
clf_y_predict = clf.predict(X_test)
print(clf_y_predict)
score = clf.score(X_test, y_test)
print(score)
```

### Import library

Import the libraries we need in sklearn, where datasets is the dataset that comes with sklearn, train_test_split is the data division training set and test set, and `LogisticRegression` is the logistic regression model in sklearn.

### Loading datasets

Load the breast cancer dataset in sklearn via datasets.load_breast_cancer(), where cancer_X is the data of each indicator to measure whether it is breast cancer or not, and cancer_y is the label data. Use shape to see the size of the data, cancer_X is the data of (569, 30) and has been preprocessed for direct use, cancer_y is the data of (569, 1).

### Dividing the training set and test set

The data set is divided into training set and test set by train_test_split() function, where test_size is the division ratio, we set it to 0.3, i.e., the training set is 70% and the test set is 30% in the data set.

### Model construction and training

There are many machine learning algorithm models available in sklearn, and it is very easy to call them in the form above to build a logistic regression model and train it with the training set data. If you want to use other algorithms, just change the algorithm model.

### Model testing

After the fourth step of model training, we use the trained model to test the data. The first line of code is the data prediction result, the second line outputs the prediction result, the third line evaluates the scores predicted by the model, and the fourth outputs the scores.

![](.\img\python_result.PNG)

The accuracy of the model prediction is 94.7% through the above test results, which shows that the logistic regression algorithm achieves better results.

## Example of Logistic Regression in R

```R
# Load data
# install.packages('mlbench')
data(BreastCancer, package="mlbench")
bc <- BreastCancer[complete.cases(BreastCancer), ]  # keep complete rows

# check the structure of this dataset.
# str(bc)

# remove id column
bc <- bc[,-1]

# convert to numeric
for(i in 1:9) {
  bc[, i] <- as.numeric(as.character(bc[, i]))
}

# Change Y values to 1's and 0's
bc$Class <- ifelse(bc$Class == "malignant", 1, 0)
bc$Class <- factor(bc$Class, levels = c(0, 1))

# Prep Training and Test data.
# install.packages('caret')
library(caret)
'%ni%' <- Negate('%in%')  # define 'not in' func
options(scipen=999)  # prevents printing scientific notations.
set.seed(100)
trainDataIndex <- createDataPartition(bc$Class, p=0.7, list = F)
trainData <- bc[trainDataIndex, ]
testData <- bc[-trainDataIndex, ]

# Class distribution of train data
table(trainData$Class)

# Down Sample
set.seed(100)
down_train <- downSample(x = trainData[, colnames(trainData) %ni% "Class"],
                         y = trainData$Class)

table(down_train$Class)


# Up Sample (optional)
set.seed(100)
up_train <- upSample(x = trainData[, colnames(trainData) %ni% "Class"],
                     y = trainData$Class)

table(up_train$Class)

# Build Logistic Model
logitmod <- glm(Class ~ Cl.thickness + Cell.size + Cell.shape, family = "binomial", data=down_train)

summary(logitmod)

pred <- predict(logitmod, newdata = testData, type = "response")
pred

# Recode factors
y_pred_num <- ifelse(pred > 0.5, 1, 0)
y_pred <- factor(y_pred_num, levels=c(0, 1))
y_act <- testData$Class

# Accuracy
mean(y_pred == y_act)  											# 94%
```

[Logistic Regression - A Complete Tutorial with Examples in R (machinelearningplus.com)](https://www.machinelearningplus.com/machine-learning/logistic-regression-tutorial-examples-r/)

The goal here is to model and predict if a given specimen (row in dataset) is `benign` or `malignant`, based on 9 other cell features. So, let’s load the data and keep only the complete cases.

the training set is 70% and the test set is 30% in the data set.

这里的目标是根据其他9个细胞特征，建立模型并预测一个给定的标本（数据集中的一行）是良性还是恶性。因此，让我们加载数据，只保留完整的病例。



# 中文版本

### 逻辑回归

![](C:\Users\prts\Desktop\Logistic_regression\img\cn.jpg)

### Python示例

**一、导入库**

```python
from sklearn import datasets
from sklearn.cross_validation import train_test_split
from sklearn.linear_model import LogisticRegression
```

导入sklearn中我们需要的库，其中datasets为sklearn中自带的数据集，train_test_split为数据划分训练集和测试集，LogisticRegression为sklearn中的逻辑回归模型。

**二、加载数据集**

```python
cancer =datasets.load_breast_cancer()
cancer_X=cancer.data
print(cancer_X.shape)
cancer_y=cancer.target
print(cancer_y.shape)
```

通过datasets.load_breast_cancer()加载sklearn中的乳腺癌数据集，其中cancer_X为衡量是否为乳腺癌的各项指标数据，cancer_y为标签数据。使用shape来查看数据的规模，cancer_X为(569, 30)的数据，且已经进行预处理可以直接使用，cancer_y为(569, 1)的数据。

**三、划分训练集和测试集**

```python
X_train,X_test,y_train, y_test=train_test_split(cancer_X,cancer_y,test_size=0.3)
```

通过train_test_split()函数将数据集划分为训练集和测试集，其中test_size为划分比例，本次我们设为0.3，即数据集中训练集为70%，测试集为30%

**四、模型构建与训练**

```python
clf = LogisticRegression()
clf.fit(X_train,y_train)
```

sklearn中为我们提供了很多机器学习算法模型，而且调用起来非常方便，形如上面的形式即可构建逻辑回归模型并使用训练集数据进行训练。如果要使用其它算法，只需更换算法模型即可。

**五、模型测试**

```python
clf_y_predict = clf.predict(X_test)
print(clf_y_predict)
score=clf.score(X_test,y_test)
print(score)
```

经过第四步模型训练后，使用训练好的模型来进行数据测试，第一行代码为数据预测结果，第二行输出预测结果，第三行为评估模型预测的分数，第四输出分数。

