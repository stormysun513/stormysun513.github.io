---
layout: post
title:  Deep Learning in Speech Recognition
date:   2017-08-23 09:14:52 -0500
categories: Project
tags: [C++, Python, Matlab, Keras]
permalink: /project/deep-learning-in-speech-recognition.html
comments: true
---

In speech processing, each word consists of syllables, and each syllable
consists of phonemes. Each time frame, with an observance (vector) mapped
to a phoneme. A speech decoder takes raw wave signals and transform them
into a sequence of phoneme labels. A set of label sequences will be selected
and rescored by a language model. A sequence with highest score from acoustic
feature may not be the correct answer. A good language model is able to fix
this problem.


Traditionally, this problem is solved using the hidden markov model (HMM).
A HMM use probability model and self-loop to model a speech with different
speed. Also, through hidden states, it may accommodate words pronounced in
different accents but has the same meaning. This approach need some feature
engineering works to help the decoder perform better job. The figure below
shows how a traditional approach is used for speech recognition and examples
for term definitions.


![tradition hmm][deepspeech-tradition-approach]{:width="80%"}


Deep learning comes into play and reduces the complexity of feature engineering.
It automatically learns the feature representation and the classifier at the
same time. We tried deep neural network only first to get frame-wise mapping
from speech signals to phoneme labels. However, we all know that speech is a
continuous signal that previous signal is highly possible to affect waveform
of next interval. Therefore, we must consider the structured characteristic
while dealing with speech recognition problem.


![feature extraction][deepspeech-dnn-extraction]{:width="80%"}


One approach is to use conditional random field that models speech signal using
probability model. We need to construct the feature vector, which its details
are illustrated in the following figure. The algorithm would like to find out
the best weight vector that has the largest dot product among training data.
We run the Viterbi inference algorithm to derive the best sequence. The error of
this sequence and the weight vector is therefore used to decide whether it meets
the most violated constraint on weight vector spaces. The problem is thus reduced
to a quadratic problem which can be solved by using some well-establish solver.


![structued svm][deepspeech-structured-svm]{:width="80%"}


After we have the sequence derived by the Viterbi algorithm. We can actually
construct the English sentence using a pre-defined weight finite state machine.
However, it does not guarantee reasonable accuracy. We are now only considering
acoustic features. However, language model from a particular scope of from a
person can be used to revise prediction result. The approach we used here was to
generate a N-best Viterbi results and rescore the sentence using LSTM-RNN model.
The input of this model is the current word and the output is the next output in
the training corpus. We can calculated the posteriori probability using this model
to compute the probability given an observation, which is the label sequences here.



Although it seems that a simple speech recognition is completed using the above
flow. However, there are some assumption and decisions have been made to simplify
the problem scope and help us to focus more on deep learning itself.


The deep neural network sometimes fails at local optimum problem, so
initialization matters. Practically, there exists unsupervised pre-training
technique on initialization. However, in this project, we are recommended to
initialize them randomly for
the simplicity and efficiency. 


Also, the frame-wise labels are usually absent. There are only human annotation
w.r.t each sentence. Practically, we forcibly align the labels to each frame
by generative models other than DNN. The alignment results are then prepared
for DNN as training targets. In this homework, you do not need to bother.
The alignments are transformed into frame-wise labels for you.

[deepspeech-dnn-extraction]: /assets/images/deepspeech-dnn-extraction.jpg#center
[deepspeech-tradition-approach]: /assets/images/deepspeech-tradition-approach.jpg#center
[deepspeech-structured-svm]: /assets/images/deepspeech-structured-svm.jpg#center
