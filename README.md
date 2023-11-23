# Swahili Automatic Speech Recognition (ASR) Experiment

## Description
This repository features code for an experimental Swahili ```Automatic Speech Recognition (ASR)``` using the Whisper model. The primary aim is to fine-tune the model on Swahili speech data and evaluate its performance on [this audio file](https://github.com/KevKibe/Finetuning-WhisperSmall-LoRA-Swahili./blob/main/example_swahili.wav).

## Fine-Tuning Approach
The fine-tuning process employs ```Parameter Efficient Fine Tuning (PEFT)```, a method that selectively fine-tunes a minimal number of model parameters while keeping the majority frozen. This results in significantly reduced computational and storage costs. The specific technique used is ```Low Rank Adaptation (LoRA)```, which introduces trainable rank decomposition matrices into each layer of the Transformer architecture.

## Original Model Transcriptions
The original model transcriptions for the [audio file](https://github.com/KevKibe/Finetuning-WhisperSmall-LoRA-Swahili./blob/main/example_swahili.wav) were ineffective, producing nonsensical outputs. Examples include:
- ```"ช์หน้ามตอนนั้นต supplementalีเปล่าขolyแขนม benimบนับมัดไอตบึกมัดเลย"```
- ```"ซูครั้งส่ารัaksiตорошоปment this is my new day.วิทย์ดึง畢อะ ๆ"```
- ```"ชุครสธานตรเป็นод mosquitoes couldn'tวMUSIC ดัดิ่งที่ต้องล่ะของคลิทฝัnd นะไม่ of"```

## Fine-Tuned Model Inference
Post fine-tuning, the transcribed text returned was ```"Shukranisana pendo poni madandefu lakinimuda haukwa upandewezu,"``` aligning closely with the correct transcription: ```"Shukrani sana pendo poni mada ndefu lakini muda hauko upande wetu."``` This demonstrates notable improvement after a test run of 100 steps.
See details in the [notebook](https://github.com/KevKibe/Finetuning-WhisperSmall-LoRA-Swahili./blob/main/Finetuning_WhisperSmall_LoRA_Swahili.ipynb).


## Potential Enhancements

Given access to improved computational resources, training the model on the entire Common Voice dataset for an extended duration (6-8 hours) holds the promise of achieving a superior Word Error Rate (WER) score. This approach is anticipated to yield enhanced performance in inference results.
