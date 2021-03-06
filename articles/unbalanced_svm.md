[Home](https://mgcodesandstats.github.io/) |
[GitHub](https://github.com/mgcodesandstats) |
[Speaking Engagements](https://mgcodesandstats.github.io/speaking-engagements/) |
[Terms](https://mgcodesandstats.github.io/terms/) |
[E-mail](mailto:contact@michael-grogan.com)

# Imbalanced Classes: Predicting Hotel Cancellations with Support Vector Machines

When attempting to build a classification algorithm, one must often contend with the issue of an unbalanced dataset.

An **unbalanced dataset** is one where there is an unequal sample size between classes, which induces significant bias into the predictions of the classifier in question.

In this particular example (available from the references section below), a support vector machine (SVM) classification model is used to classify hotel booking customers in terms of cancellation risk, i.e. **1** if the model predicts that the customer will cancel their booking, **0** if the customer will follow through with the booking.

The H1 dataset is used to train and validate the model, while the predictions from the resulting model are then tested using the H2 data.

In this particular dataset, the sample size for the non-cancellation class (0) is significantly greater than the cancellation class (1). In a previous example, this was dealt with by removing numerous **0** entries in order to have an equal sample size between the two classes. However, this is not necessarily the best approach, as many data points are discarded during this process.

Instead, the SVM model can be modified to penalise wrong predictions on the minor class. Let's see how this affects the analysis.

The identified features to be included in the analysis using both the ExtraTreesClassifier and forward and backward feature selection methods are as follows:

- Lead time
- Country of origin
- Market segment
- Deposit type
- Customer type
- Required car parking spaces
- Arrival Date: Year
- Arrival Date: Month
- Arrival Date: Week Number
- Arrival Date: Day of Month

# What Is an SVM?

An SVM is a supervised learning model that can be used for both classification and regression tasks.

The SVM model provides an assessment of the importance of each training point when defining the decision limit between two classes.

![svm-plane.png](svm-plane.png)

The few training points selected that lie on the decision boundary between the two classes are called support vectors.

## Precision vs. Recall and f1-score

When comparing the accuracy scores, we see that numerous readings are provided in each confusion matrix.

However, a particularly important distinction exists between **precision** and **recall**. 

```
Precision = ((True Positive)/(True Positive + False Positive))

Recall = ((True Positive)/(True Positive + False Negative))
```

The two readings are often at odds with each other, i.e. it is often not possible to increase precision without reducing recall, and vice versa.

An assessment as to the ideal metric to use depends in large part on the specific data under analysis. For example, cancer detection screenings that have false negatives (i.e. indicating patients do not have cancer when in fact they do), is a big no-no. Under this scenario, recall is the ideal metric.

However, for emails - one might prefer to avoid false positives, i.e. sending an important email to the spam folder when in fact it is legitimate.

The f1-score takes both precision and recall into account when devising a more general score.

Which would be more important for predicting hotel cancellations?

Well, from the point of view of a hotel - they would likely wish to identify customers who are ultimately going to cancel their booking with greater accuracy - this allows the hotel to better allocate rooms and resources. Identifying customers who are not going to cancel their bookings may not necessarily add value to the hotel's analysis, as the hotel knows that a significant proportion of customers will ultimately follow through with their bookings in any case.

## SVM and Unbalanced Datasets

The relevant features as outlined above are included for determining whether the customer will cancel their booking.

```
y1 = y
x1 = np.column_stack((leadtime,countrycat,marketsegmentcat,deposittypecat,customertypecat,rcps,arrivaldateyear,arrivaldatemonthcat,arrivaldateweekno,arrivaldatedayofmonth))
x1 = sm.add_constant(x1, prepend=True)
```

The data is then split into training and validation data:

```
x1_train, x1_val, y1_train, y1_val = train_test_split(x1, y1, random_state=0)
```

A 'balanced' class weight can be added to the SVM configuration, which adds a greater penalty to incorrect classifications on the minor class (in this case, the cancellation class).

```
from sklearn import svm
clf = svm.SVC(gamma='scale', 
            class_weight='balanced')
clf.fit(x1_train, y1_train)  
prclf = clf.predict(x1_val)
prclf
```

Here is the classification performance of this model on the validation set:

```
[[5142 2124]
 [ 865 1884]]
              precision    recall  f1-score   support

           0       0.86      0.71      0.77      7266
           1       0.47      0.69      0.56      2749

    accuracy                           0.70     10015
   macro avg       0.66      0.70      0.67     10015
weighted avg       0.75      0.70      0.72     10015
```

Recall for class 1 comes in at 69%, while the f1-score accuracy comes in at 70%. Now, let's test the prediction performance on H2 (the test set).

```
[[25217 21011]
 [ 8436 24666]]
              precision    recall  f1-score   support

           0       0.75      0.55      0.63     46228
           1       0.54      0.75      0.63     33102

    accuracy                           0.63     79330
   macro avg       0.64      0.65      0.63     79330
weighted avg       0.66      0.63      0.63     79330
```

We see that recall for class 1 is now up to 75%, while f1-score accuracy comes in at 63%. Notably, we can see that the f1-score is lower on the test set, but the recall is now higher than on the validation set.

In this regard, if it is assumed that false positives are more tolerable than false negatives in this situation - then one could argue that the model has performed quite well on this basis.

## Conclusion

In this example, we have seen how support vector machines can be used to handle unbalanced datasets, and how to interpret confusion matrices for classification accuracy.

The datasets and notebooks for this example are available at the [MGCodesandStats GitHub repository](https://github.com/MGCodesandStats/hotel-modelling), along with further research on this topic.

## References

[- Elite Data Science: How to Handle Imbalanced Classes in Machine Learning](https://elitedatascience.com/imbalanced-classes)
