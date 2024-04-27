# Solutions Report

## Student information

- Name: Viktor Kovalev
- Email: vi.kovalev@innopolis.university
- Codalab nickname: blueberry13
- github link: [github.com/blueberry13-8/nlp-assignments/tree/main/A3](https://github.com/blueberry13-8/nlp-assignments/tree/main/A3)

## Data

I took data from codalab and `RuNNE` from huggingface, therefore, in total it was 980 train samples and 
70 evaluation samples. I used huggingface library `datasets` to process data. Firstly, I format `RuNNE`
to the same format as codalab dataset. More precisely, in codalab end of entity is inclusive, in the `RuNNE` 
exclusive. After that I used tokenizer of choosed model and got tokenized versions of texts, labeled it
by ners and that's all.

## Solution 1. 29 classifiers.

### Model Architecture

One common tiny bert for feature extraction with 29 classifiers for each ner class. Therefore, I tried 
to predict nested ner. It's good idea for specific dataset, because we have not so many classes. In other
situations it's bad solution. It's scalable, but we don't want to train 100+ classifiers (but we can XD).

### Results

I trained model for 15 epochs and here is the result:
1. dev set: `f1 = 0.02`
2. test set: `f1 = 0.08`

At the end I get bad results. I guess it's because of BPE tokenization. I don't kwon how to format data
in better way(

## Solution 2. Bert for ~~nested~~ usuall ner.

### Model Architecture

`KoichiYasuoka/bert-base-russian-upos` from hugging face which was pretrained in russian language. 
Bert was used for feature extraction. In the end was classifier which output tensor of shape 
`[batch_size, seq_len, num_classes]`, where `num_classes` is equal to 59, because of BIO tags.
Therefore, it wasn't nested ner, this solution only for usuall ner.

### Results

I got following results in my local evaluation stage on the evaluation set: `f1 = 0.709213`. It's pretty good results. Also I look at the predictions of the model and it was accurate enough. But on the codalab I got bad score. 

I looked at evaluator from the github repository which you provide in the competition and found how it's scoring. Evaluator use just exact comparison, which is pretty bad for such task. For instance if my model output:
```bash
{"id": 519, "ners": [[0, 8, PERSON]]}
```
But the answer is:
```bash
{"id": 519, "ners": [[0, 6, PERSON]]}
```
The score will be 0. In my opinion, it's not the good way for scoring (I am just offendent by low score student T_T)

I trained model for 18 epochs and here is the result:
1. dev set: `f1 = 0.12`
2. test set: `f1 = 0.16`

Results still bad because of the same issue.