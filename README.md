# Llava: Fine-tuning a Vision Language Model for Visual Questions Answering tasks on medical images
Llava is a open source multimodal vision language model, that has been trained by fine-tuning LlamA/Vicuna on GPT-generated multimodal instruction-following data. The research paper can be found at https://arxiv.org/abs/2310.03744.

This repository explores the possibilities of fine-tuning the pretrained Llava-1.5 model on custom data to adopt it for different specialized purposes. Parrticularly, medical imaging data has been used to check the fficiency and accuracy of Llava in medical applications, once fine-tuned on such data.

# Data Procurement
The data was collected from the open source CheXpert Plus dataset provided by the Stanford AIMI. The dataset contains X-Ray images of several patients belonging to 14 different disease classes. Also accompanying the X-Ray scans is a radiology report manually written by a human doctor after careful analysis of the particular X-ray. 

The data can be found at https://stanfordaimi.azurewebsites.net/datasets/5158c524-d3ab-4e02-96e9-6ee9efc110a1

# Data Processing and Subsequent Dataset formation
The X-ray images and their corresponding radiology reports were extracted from the downloaded dataset. I took reference from the llava-instruct-mix-vsft dataset available on Hugging Face as to what format the dataset must be made into. The format of the reference dataset is shown in . Now the task was to convert my data into the question-answer format. 
This was achieved using the OpenAI API. The radiology report was fed using the API key to ChatGPT, which then generated 5 sets of questions and answers for that report. These questions and answers were then used to make a dataset similar to the llava-instruct-mix-vsft. A sample is shown in .

These dictionaries were appended into an array to form the final dataset.

A LLavaDataCollator class was formed, which organizes text and image data into a format suitable for vision language models. First, it gathers messages from each data point and structures them into a text template for uniform processing. Each example’s primary image is also selected and collected. The processor then combines these text and image entries into a structured batch, applying padding to ensure each entry aligns in size for batch processing. Special tokens are added for images and padding, which help the model differentiate between text and visual elements. Padding tokens are excluded from the model’s loss calculation by setting them to -100, allowing the model to focus on meaningful inputs. This batch format is then prepared for model training or inference.

# Training the Model
The TrainingArguments class here configures key parameters for model training. It specifies the directory to store output logs and checkpoints, as well as a fine-tuned learning rate to control the model’s learning pace. Training is set up with a batch size of 1 per device, and gradients accumulate over each batch step to maintain stability. The training process logs metrics every 5 steps to monitor progress. Only one training epoch is set, limiting the number of complete passes over the dataset. gradient_checkpointing is enabled to save memory by storing fewer activations, and mixed-precision training with 16-bit floats (fp16) is also enabled to speed up computation. remove_unused_columns is set to false, preserving all data columns for versatility in input handling. Most of the parameters have been set with an aim to reduce computational cost as much as possible.

The LoraConfig configuration defines parameters for Low Rank Adaptation (LoRA), a method used to reduce memory and computation needs when fine-tuning large language models. Here, r=64 sets the rank, determining the reduced dimensionality for adaptation matrices, balancing between expressiveness and efficiency. The lora_alpha=16 parameter controls the scaling factor, adjusting the impact of the low-rank updates during training. target_modules specifies which modules within the model will use LoRA, allowing focused, efficient adaptations on specific layers or components, thereby minimizing changes to the overall model and optimizing resource usage.

The SFTTrainer configuration defines the parameters for supervised fine-tuning of the model. It specifies the model to be fine-tuned and uses previously defined training arguments to control training behavior, such as batch size, learning rate, and logging steps. Low Rank Adaptation (LoRA) is applied via peft_config, enabling efficient model adaptation. The dataset_text_field provides a placeholder for the expected text field in the dataset, ensuring compatibility even if the data format differs slightly. The tokenizer and data collator are included to manage text processing and batch creation. dataset_kwargs allows for skipping dataset preparation steps, optimizing setup time when data is already structured correctly. This setup fine-tunes the model with a focus on memory efficiency and targeted layer updates.


