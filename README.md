# Llava: Fine-tuning a Vision Language Model for Visual Questions Answering tasks on medical images
Llava is a open source multimodal vision language model, that has been trained by fine-tuning LlamA/Vicuna on GPT-generated multimodal instruction-following data. The research paper can be found at https://arxiv.org/abs/2310.03744.

This repository explores the possibilities of fine-tuning the pretrained Llava-1.5 model on custom data to adopt it for different specialized purposes. Parrticularly, medical imaging data has been used to check the fficiency and accuracy of Llava in medical applications, once fine-tuned on such data.

# Data Procurement
The data was collected from the open source CheXpert Plus dataset provided by the Stanford AIMI. The dataset contains X-Ray images of several patients belonging to 14 different disease classes. Also accompanying the X-Ray scans is a radiology report manually written by a human doctor after careful analysis of the particular X-ray. 

The data can be found at https://stanfordaimi.azurewebsites.net/datasets/5158c524-d3ab-4e02-96e9-6ee9efc110a1

# Data Processing and Subsequent Dataset formation


