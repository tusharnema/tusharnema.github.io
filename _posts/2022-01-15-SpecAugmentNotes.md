---
title: "SpecAugment"
date: 2022-01-15
tags: [Machine learning]
header:
  image: ""
excerpt: "Augmentation Technique for Audio"
mathjax: "true"
---

SpecAugment Notes

**Paper Link**: https://arxiv.org/pdf/1904.08779.pdf

- In the absence of an adequate volume of training data, it is possible to increase the effective size of existing data through the process of data augmentation.
  - In the case of speech recognition, augmentation traditionally involves deforming the audio waveform used for training in some fashion (e. g., by speeding it up or slowing it down), or adding background noise. 
    - For Example we use speed perturbation and volume perturbation in Kaldi as augmentation techniques.
- This has the effect of making the dataset effectively larger, as multiple augmented versions of a single input is fed into the network over the course of training, and also helps the network become robust by forcing it to learn relevant features.
- **Problem with current Augmentation Techniques:![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.001.png)**

Existing conventional methods of augmenting audio input introduces additional computational cost and sometimes requires additional data.

Do not happen on the fly in the pipeline.

**Approach**:

We take a new approach to augmenting audio data, treating it as a visual problem rather than an audio one. Instead of augmenting the input audio waveform as is traditionally done, SpecAugment applies an augmentation policy directly to the audio spectrogram (i.e., an image representation of the waveform). *This method is simple, computationally cheap to apply, and does not require additional data.*

*Augmenting the spectrogram itself, rather than the waveform data. Since the augmentation is applied directly to the input features of the network, it can be run online during training without significantly impacting training speed.![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.002.png)*

A waveform is typically converted into a visual representation (in our case, a log mel spectrogram) before being fed into a network.![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.003.png)![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.004.png)

![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.005.png) SpecAugment modifies the spectrogram by warping it in the time direction, masking blocks of consecutive frequency channels, and masking blocks of utterances in time. These augmentations have been chosen to help the network to be robust against deformations in the time direction, partial loss of frequency information and partial loss of small segments of speech of the input.![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.006.png)

![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.007.png)

The log mel spectrogram is augmented by warping in the time direction, and masking (multiple) blocks of consecutive time steps (vertical masks) and mel frequency channels (horizontal masks). The masked portion of the spectrogram is displayed in purple for emphasis.![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.008.png)

**Results:**

![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.009.png)

Performance of networks on the test sets of LibriSpeech with and without augmentation. The LibriSpeech test set is divided into two portions, test-clean and test-other, the latter of which contains noisier audio data.![](/images/SpecAugment_Images/Aspose.Words.9d3e145b-62a3-4d02-b576-310dd496535c.010.png)

**Important Observations:**

- SpecAugment prevents the network from over-fitting by giving it deliberately corrupted data.
  - This happens on the fly and computationally cheap.
    - Time warping is applied via the function sparse image warp of tensorflow.
      - Frequency masking is applied so that f consecutive mel frequency channels [f0 , f0 + f ) are masked.
        - We use a learning rate schedule in which we ramp-up, hold, then exponentially decay the learning rate until it reaches 1/100 of its maximum value.
