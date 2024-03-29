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
Post fine-tuning, the transcribed text returned was ```"Shukranisana pendo poni madandefu lakinimuda haukwa upandewezu,"``` aligning closely with the correct transcription: ```"Shukrani sana pendo poni mada ndefu lakini muda hauko upande wetu."``` This demonstrates notable improvement after a test run of 100 steps(30 mins).
See details in the [notebook](https://github.com/KevKibe/Finetuning-WhisperSmall-LoRA-Swahili./blob/main/Finetuning_WhisperSmall_LoRA_Swahili.ipynb).

## To get Inference
```
!pip install -q git+https://github.com/huggingface/peft.git@main
!pip install accelerate 
!pip install bitsandbytes==0.37
!pip install torch

from transformers import (
    AutomaticSpeechRecognitionPipeline,
    WhisperForConditionalGeneration,
    WhisperTokenizer,
    WhisperProcessor,
)
from peft import PeftModel, PeftConfig
import torch

peft_model_id = "KevinKibe/swahili-asr-whisper-small-LoRA-finetune-100steps"
language = "sw"
task = "transcribe"
peft_config = PeftConfig.from_pretrained(peft_model_id)
model = WhisperForConditionalGeneration.from_pretrained(
    peft_config.base_model_name_or_path, load_in_8bit=True, device_map="auto"
)

model = PeftModel.from_pretrained(model, peft_model_id)
tokenizer = WhisperTokenizer.from_pretrained(peft_config.base_model_name_or_path, language=language, task=task)
processor = WhisperProcessor.from_pretrained(peft_config.base_model_name_or_path, language=language, task=task)
feature_extractor = processor.feature_extractor
forced_decoder_ids = processor.get_decoder_prompt_ids(language=language, task=task)
pipe = AutomaticSpeechRecognitionPipeline(model=model, tokenizer=tokenizer, feature_extractor=feature_extractor)


def transcribe(audio):
    with torch.cuda.amp.autocast():
        text = pipe(audio, generate_kwargs={"forced_decoder_ids": forced_decoder_ids}, max_new_tokens=255)["text"]
    return text

transcribe("path to file")
```
## Potential Enhancements

Given access to improved computational resources, training the model on the entire Common Voice dataset for an extended duration (6-8 hours) holds the promise of achieving a superior Word Error Rate (WER) score. This approach is anticipated to yield enhanced performance in inference results.
