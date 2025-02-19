# TextGraphs-17

This repository presents the 2nd place solution in the shared task [TextGraphs-17](https://sites.google.com/view/textgraphs2024/home/shared-task): Graph-based Methods for Natural Language Processing.

 ### Description
The shared task is to select a KG entity (out of several candidates) which correspond to an answer given a textual question. The specificity of the task, is that for each question-answer (Q-A) pair not only textual Q-A pair is given but also a graph of shortest paths in the KG from entities in query to the LLM-generated candidate entity (including links of the intermediate nodes). This way, participants easily may experiment with various strategies of text-graph modality fusion for the given task in a controllable manner. 

### Method

The baseline is obtained with GPT-4 with CoT prompting and provided set of answers.

#### CoT prompt:
<img src="Images/base_prompt.png" alt="drawing" width="450" height ="300"/>

#### Answer example:

<img src="Images/qa_example.png" alt="drawing" width="450" height ="300"/>

### Main tricks

#### Fixing multiple answers 

1) Among identical answers select the one with the minimal length of subgraph
2) Add Wikidata description of the answer and select the answer with the largest cosine similarity between mean of the fastText embeddings of the question and answer
3) Use [Konstruktor](https://github.com/s-nlp/konstruktor) to rank relations of the question entities (collected from the extracted subgraphs) and filter out wrong answers

#### Fixing NA answers
1) Ask LLM to rephrase the question to make it clearer for LLM to answer
2) Add Wikidata descriptions of entities as a context to the prompt:

<img src="Images/qa_example_v2.png" alt="drawing" width="450" height ="400"/>

### Requirements

All code is runable with requirements installed from: 

`requirements.txt`

### Code

In the notebook `TextGraphs_posteval_github_best.ipynb` is the code for all experiemnts tricks with data files stored in the `Data` folder.

In the notebook `TextGraphs_GPT4_prompt_example.ipynb` is the example of the base prompt to GPT4.

In the notebook `Wiki_collect_answer_description.ipynb` you may find a function for the collection of the Wikidata description of the entity.

In the notebook `Ranking.ipynb` you may find prepairing inference file to [Konstruktor](https://github.com/s-nlp/konstruktor) for filtering out multiple identical answers by dropping the paths with unrelevant relations of question entities.

### Citations

Please cite the paper and star this repo if you use the method and find it interesting/useful, thanks! Feel free to contact lysyuk.m.v@gmail.com or open an issue if you have any questions.

```
@inproceedings{lysyuk2024skoltech,
  title={Skoltech at TextGraphs-17 Shared Task: Finding GPT-4 Prompting Strategies for Multiple Choice Questions},
  author={Lysyuk, Maria and Braslavski, Pavel},
  booktitle={Proceedings of the Graph-based Methods for Natural Language Processing (TextGraphs)},
  year={2024}
}
```

# How Much Knowledge Can You Pack into a LoRA Adapter without Harming LLM?

### Description

The performance of Large Language Models~(LLMs) on many tasks is greatly limited by the knowledge learned during pre-training and stored in the model's parameters. Low-rank adaptation (LoRA) is a popular and efficient training technique for updating or domain-specific adaptation of LLMs. In this study, we investigate how new facts can be incorporated into the LLM using LoRA without compromising the previously learned knowledge. We fine-tuned Llama-3.1-8B-instruct using LoRA with varying amounts of new knowledge. Our experiments have shown that the best results are obtained when the training data contains a mixture of known and new facts. However, this approach is still potentially harmful because the model's performance on external question-answering benchmarks declines after such fine-tuning. When the training data is biased towards certain entities, the model tends to regress to few overrepresented answers. In addition, we found that the model becomes more confident and refuses to provide an answer in only few cases. These findings highlight the potential pitfalls of LoRA-based LLM updates and underscore the importance of training data composition and tuning parameters to balance new knowledge integration and general model capabilities.

### Data 

For creating data samples we follow the strategy of the [head-to-tail pipeline](https://github.com/facebookresearch/head-to-tail). Namely, the script for creating questions and answers using [Dbpedia script](https://github.com/facebookresearch/head-to-tail).

To collect the triples from the KG relevant to the questions creation, please, download [Dbpedia dump](https://databus.dbpedia.org/dbpedia/mappings/mappingbased-objects). 

To reproduce the dataset by your own follow the scipt Data_collection.ipynb

The dataset used in the paper with the precollected triples and questions created by the corresponding templates can be downloaded from [here](https://drive.google.com/file/d/1pCtfRlvBW769384AgmfNBpIU8OmftfKd/view?usp=sharing).

The questions with the labelled knowledge categories for the Llama-3.1-8B-Instruct model can be downloaded from [here](https://drive.google.com/file/d/1-NDeTa8TMRNY9UIsIqtI-Iw4vq-rda35/view?usp=sharing).

### Data analysis

To analyse the reasons of the shifts (domain-based, target-based, exploded, non-refusion) use the notebook Knowledge_spillovers_analysis.ipynb.

As an input use two train data files listed in the Data section as well as the trained LORA model.

For some examples for you to test, you may donwload some weights of the trained LORA models: [50 Unknown+ 0 HighlyKnown](https://drive.google.com/file/d/18aTHDFA6RtxhOUK9L_D27toPj94VjeNd/view?usp=sharing),  [50 Unknown+ 1 HighlyKnown](https://drive.google.com/file/d/1ya5tE5XBLC4n7PP01_v9z00Bzy_2tQj-/view?usp=sharing), [50 Unknown + 10 HighlyKnown](https://drive.google.com/file/d/1o1SKVEiTbpMA7_PeLV5DXI_oI2wxYeiQ/view?usp=sharing).



