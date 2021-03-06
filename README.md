# Semi-supervised Learning with Generative Adversarial Networks (GANs)

Modern deep learning classifiers require a large volume of labeled
samples to be able to generalize well. GANs have shown a lot of
potential in *semi-supervised learning* where the classifier can obtain
good performance with very few labeled data *(Salimans et. al., 2016)*.

## Overview

To train a ![](http://latex.codecogs.com/gif.latex?k)-class classifier with a small number of labeled samples, discriminator (D) in a GAN's game should be replaced with a
![](http://latex.codecogs.com/gif.latex?%28k&plus;1%29)-classiifer where it receives a data point ![](http://latex.codecogs.com/gif.latex?x) as input and
outputs a ![](http://latex.codecogs.com/gif.latex?%28k&plus;1%29)-dimensional vector of
logits ![](http://latex.codecogs.com/gif.latex?%24%5C%20%5Cleft%5C%7B%20l_%7B1%7D%2Cl_%7B2%7D%2C%5C%20%5Cldots%2C%5C%20l_%7Bk%7D%2C%5C%20l_%7Bk%20&plus;%201%7D%20%5Cright%5C%7D%24).
These logits can then be transferred into class probabilities, where:

<p align="center">
<img  alt="" src="http://latex.codecogs.com/gif.latex?p_%7Bmodel%7D%28y%3Dk&plus;1%7Cx%29%3D%5Cfrac%7Bexp%28l_%7Bk&plus;1%7D%29%7D%7B%5Csum_%7Bj%3D1%7D%5E%7Bk&plus;1%7D%7Bexp%28l_%7Bj%7D%29%7D%7D">
</p>

provides the probability that ![](http://latex.codecogs.com/gif.latex?x) is fake.

<p align="center">
<img  alt="" src="http://latex.codecogs.com/gif.latex?p_%7Bmodel%7D%28y%3Di%7Cx%2C%20i%3Ck&plus;1%29%3D%5Cfrac%7Bexp%28l_%7Bi%7D%29%7D%7B%5Csum_%7Bj%3D1%7D%5E%7Bk&plus;1%7D%7Bexp%28l_%7Bj%7D%29%7D%7D">
</p>

provides the probability that ![](http://latex.codecogs.com/gif.latex?x) is real and belongs to class ![](http://latex.codecogs.com/gif.latex?i). Now,
the loss of discriminator can be written as:

<p align="center">
<img  alt="" src="http://latex.codecogs.com/gif.latex?%24%24L_%7BD%7D%20%3D%20%5C%20L_%7BD_%7B%5Ctext%7Bsupervised%7D%7D%7D%20&plus;%20L_%7BD_%7B%5Ctext%7Bunsupervised%7D%7D%7D%24%24">
</p>

where:

<p align="center">
<img  alt="" src="http://latex.codecogs.com/gif.latex?%24%24L_%7BD_%7B%5Ctext%7Bsupervised%7D%7D%7D%20%3D%24%24-%20%5Cmathbb%7BE%7D_%7Bx%2C%5C%20y%5C%20%5Csim%20%5C%20p_%7B%5Ctext%7Bdata%7D%7D%7D%5Clog%5Cleft%5Clbrack%20p_%7B%5Ctext%7Bmodel%7D%7D%28y%5C%20%3D%20%5C%20i%7Cx%2C%5C%20i%20%3C%20k%20&plus;%201%29%20%5Cright%5Crbrack%24%24">
</p>

is the standard supervised learning loss function given that the data is real and:

<p align="center">
<img  alt="" src="http://latex.codecogs.com/gif.latex?%24%24L_%7BD_%7B%5Ctext%7Bunsupervised%7D%7D%7D%20%3D%20%5C%20-%20%5Cmathbb%7BE%7D_%7B%5Ctext%7Bx%5C%20%7D%20%5Csim%20%5C%20p_%7B%5Ctext%7Bdata%7D%7D%7D%5Clog%5Cleft%5Clbrack%201%20-%20%5C%20p_%7B%5Ctext%7Bmodel%7D%7D%5Cleft%28%20y%20%3D%20k%20&plus;%201%20%5Cmiddle%7C%20x%20%5Cright%29%20%5Cright%5Crbrack%20-%20%5C%20%5Cmathbb%7BE%7D_%7B%5Ctext%7Bx%5C%20%7D%20%5Csim%20%5Ctext%7B%5C%20G%7D%7D%5Clog%5Cleft%5Clbrack%20p_%7B%5Ctext%7Bmodel%7D%7D%28y%20%3D%20k%20&plus;%201%7Cx%29%20%5Cright%5Crbrack%24%24">
</p>

is the standard GAN's game-value where:

<p align="center">
<img  alt="" src="http://latex.codecogs.com/gif.latex?%24D%5Cleft%28%20x%20%5Cright%29%20%3D%20%5C%201%20-%20%5C%20p_%7B%5Ctext%7Bmodel%7D%7D%5Cleft%28%20y%20%3D%20k%20&plus;%201%20%5Cmiddle%7C%20x%20%5Cright%29%24">.
</p>

Now, let's ![](http://latex.codecogs.com/gif.latex?f%28x%29) denote the activations on an intermediate layer of
discriminator. The *feature matching* loss of generator can be defined
as:

<p align="center">
<img  alt="" src="http://latex.codecogs.com/gif.latex?%24%24L_%7BG_%7B%5Ctext%7Bfeature%5C%20matching%7D%7D%20%3D%20%5C%20%5Cleft%5C%7C%20%5Cmathbb%7BE%7D_%7B%5Ctext%7Bx%5C%20%7D%20%5Csim%20%5C%20p_%7B%5Ctext%7Bdata%7D%7D%7Df%5Cleft%28%20x%20%5Cright%29%5C%20-%20%5C%20%5Cmathbb%7BE%7D_%7B%5Ctext%7Bx%5C%20%7D%20%5Csim%20%5Ctext%7B%5C%20G%7D%7Df%5Cleft%28%20x%20%5Cright%29%20%5Cright%5C%7C_%7B2%7D%5E%7B2%7D.%7D%24%24">
</p>

Feature matching has shown a lot of potential in semi-supervised
learning. The goal of feature matching is to push the generator to
generate data that matches the statistics of real data. Discriminator is
used to specify those statistics as it naturally learns to find features
that are most discriminative of real data versus data generated by the
current model.

In this code, I combined ![](http://latex.codecogs.com/gif.latex?%24%24L_%7BG_%7B%5Ctext%7Bfeature%5C%20matching%7D%7D%7D%24%24) with the
known generator cost that maximizes the log-probability of discriminator being mistaken:

<p align="center">
  <img src="http://latex.codecogs.com/gif.latex?%24%24L_%7BG_%7Bcross%20-%20entropy%7D%7D%20%3D%20-%20%5Cmathbb%7BE%7D_%7B%5Ctext%7Bx%5C%20%7D%20%5Csim%20%5Ctext%7B%5C%20G%7D%7D%5Clog%5Cleft%5Clbrack%201%20-%20%5C%20p_%7B%5Ctext%7Bmodel%7D%7D%5Cleft%28%20y%20%3D%20k%20&plus;%201%20%5Cmiddle%7C%20x%20%5Cright%29%20%5Cright%5Crbrack%24%24">.
</p>

So, the loss of generator can be written as:

<p align="center">
  <img src="http://latex.codecogs.com/gif.latex?%24%24L_%7BG%7D%20%3D%20%5C%20%7BL_%7BG_%7B%5Ctext%7Bfeature%5C%20matching%7D%7D%7D%20&plus;%20L_%7BG_%7Bcross%20-%20entropy%7D%7D%7D_%7B%5C%20%7D.%24%24">
</p>

## Results

Table below shows cross-validation accuracy of semi-supervised
learning GAN for 1000 epochs when 10% and 100% of MNIST data is
labeled. 


| 10% labeled data           | 100% labeled data  |
|----------------------------| -------------------|
| 0.9255                     | 0.945              |



Figure below shows cross-validation accuracy for 1000 epochs when
10% of data is labeled. As can be seen here, training has not yet reached a
plateau which indicates further training could provide higher accuracy.

<p align="center">
<img width="572" alt="" src="https://user-images.githubusercontent.com/20761298/32979454-a585b54c-cca9-11e7-9b25-604e807036f9.png">
</p>

Figures below show some generated samples at different epochs of
training when 10% of data is labeled:

![](https://user-images.githubusercontent.com/20761298/32980242-e8a44854-ccb6-11e7-9408-4e64a3e307e2.png)


## Reference:

Salimans, T., Goodfellow, I., Zaremba, W., Cheung, V., Radford, A., and
Chen, X. (2016). Improved Techniques for Training GANs. *In advances in
Neural Information Processing Systems (NIPS)*, pages 2226-2234
(<http://papers.nips.cc/paper/6125-improved-techniques-for-training-gans.pdf>)
