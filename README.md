# SkinLesionsClassification

Skin Lesions Classification through Deep Learning methods, based on DenseNet-121 and ResNet-101 Convolutional Neural Networks

## Description

This project comprises Deep Learning approaches to classify skin lesions of ISIC 2018 dataset, which includes lesions from 7 different classes as shown in the next image.

<img src="/Images/hier_skin.png" width="720">

As shown, these lesions Skin can be organized in a hierarchical structure, where in the first level the melanocytic and non-melanocytic lesions are split. In each of these sub-groups the benign and malignant lesions are further discriminated.

Thus, in this project we implement and compare the results of three different approaches to diagnose skin lesions, based in
deep learning:

1. a flat approach, where the inference is done in a single decision.

2. a hierarchical approach that explores the taxonomic structure of skin lesions, where the inference for each example involves more than one decision.

3. a mixed model, which combines these approaches, in order to capture the strengths of each previous one. 

**Conclusion:** We verified that the hierarchical model was affected by error propagation of intermediate decisions, in comparison with the flat classifier. On the other hand, the mixed model significantly improves the results of both approaches.

## Flat Classifier

The flat classifier consists of the CNN backbone, followed by a global average pooling operation, and a single softmax layer with the same size as the number of skin lesion classes in the ISIC 2018 dataset. 

This model performs a **single decision** when predicting the class of an image.


<img src="/Images/flat.png" width="520">

## Hierarchical Classifier

The hierarchical model exploits the hierarchical organization of skin lesions. When predicting the category of each skin lesion image, it makes **intermediate decisions** before providing the final output. This approach closely mimics the procedure followed by a dermatologist. There are three levels of decision: 
1. melanocytic vs. non-melanocytic; 
2. benign vs.malignant;
3. final diagnosis.

The inference stage is illustrated in the next image. Since there are only one melanocytic-benign class, and only one non-melanocytic-malignant class we can reduce the number of decision levels from 3 to 2 for melanocytic lesions.


<img src="/Images/hier_inference.png" width="620">

Two different strategies were investigated:

1. **Non-Shared Features**: a model with five different networks, each one with their own CNN backbone as feature extractor. These networks are trained independently, with specific training sets. For instance, classifier (c) is trained only with all the non-melanocytic lesions.

2. **Shared Features**: relies on feature sharing approach, where a single CNN backbone is used for the five classifiers.

## Mixed Model

During the inference stage, the hierarchical model has to make more than one decision, to reach the final class. If at least one decision is incorrect, the final diagnose will be automatically incorrect. Therefore, it is very important that in each decision, the hierarchical model is very confident, to prevent error propagation.

Our approach to assess the confidence of each decision was to evaluate the difference between the two largest softmax probabilities at each hierarchical stage. If the difference is large, we assume that the classifier is confident in the output class, however, if the difference is small, we assume that the classifier is not confident and it is undecided between those
two classes.

Each of the five classifiers is associated with a confidence threshold ηi ∈ [0, 100%], where i ∈ {a, b, c, d, e}. During the inference stage, if at any classifier, the difference between the two largest softmax probabilities is less than its threshold, the image is sent to the flat model, otherwise it is classified with the hierarchical model. This mixed model is illustrated
in the next figure. Although the second decision of the mixed model is correct, it is not confident enough, using ηc = 30%. Therefore, the lesion is diagnosed with the flat model that correctly predicts the class.

<img src="/Images/mixed.png" width="820">

## Results

The next image shows the confusion matrices obtained with the following approaches from top-left: **flat**, **hier-non-shared**, **hier-shared**, and **mixed**


<img src="/Images/conf_matrices.png" width="820">
