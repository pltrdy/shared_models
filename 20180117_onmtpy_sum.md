## Trained model
* [epoch 16, ppl 8.89, acc 55.57](https://goo.gl/Q9AWoR) [(zip)]


---

Using OpenNMT-py af24c9b58417ce154c86496d37d46e5872b52179

## Preprocessing
1. [A. See's preprocessing](https://github.com/OpenNMT/cnn-dailymail) 
2. @mataney 's script to get text file from TF bins ([gist here](https://gist.github.com/mataney/67cfb05b0b84e88da3e0fe04fb80cfc8))
3. I'm then replacing `<s>`/`</s>` tokens from target files to `<t>`/`</t>`
4. OpenNMT preprocessing as follows:
```
  python preprocess.py \
      -train_src $data/train.src.txt \
      -train_tgt $data/train.tgt.repeosbos.txt \
      -valid_src $data/valid.src.txt \
      -valid_tgt $data/valid.tgt.repeosbos.txt \
      -save_data $root/data \
      -src_seq_length 10000 \
      -tgt_seq_length 10000 \
      -src_seq_length_trunc 400 \
      -tgt_seq_length_trunc 100 \
      -dynamic_dict \
      -share_vocab \
      -save_data $root/data 
```
(`*.repeos.txt` files are those with `<t>` and `</t>` )

## Training 
```
    python train.py -save_model $root/model \
                    -copy_attn \
                    -global_attention mlp \
                    -word_vec_size 128 \
                    -rnn_size 512 \
                    -layers 1 \ 
                    -encoder_type brnn \
                    -epochs 16 \
                    -seed 777 \
                    -batch_size 16 \
                    -max_grad_norm 2 \ 
                    -share_embeddings \
                    -dropout 0. \
                    -gpuid $gpu \
                    -optim adagrad \
                    -learning_rate 0.15 \
                    -adagrad_accumulator_init 0.1 \
                    -data $root/data 

```



## Translation 
```
  best_model=$(ls -lsrt $root/model*.pt | tail -n 1 | awk '{print $NF}')
    python translate.py -model "$best_model" \
                      -gpu "$gpu" \
                      -batch_size 16 \
                      -verbose \
                      -dynamic_dict \
                      -share_vocab \
                      -beam_size 5 \
                      -min_length 5 \
                      -alpha 0.9 -beta 0.25 \
                      -src $data/test.src.txt
```

## Output example
```
<t> mentally ill inmates in miami are housed on the `` forgotten floor '' </t> <t> judge steven leifman says most are there as a result of `` avoidable felonies '' </t> <t> while cnn tours facility , patient shouts : `` i am the son of the president '' </t> <t> leifman says the system is unjust and he 's fighting for change . </t>
<t> harry potter star daniel radcliffe gets # 20m fortune as he turns 18 monday . </t> <t> young actor says he has no plans to fritter his cash away . </t> <t> radcliffe 's earnings from first five potter films have been held in trust fund . </t> 
<t> new : `` i thought i was going to die , '' driver says . </t> <t> man says pickup truck was folded in half ; he just has cut on face . </t> <t> driver : `` i probably had a 30 - , 35-foot free fall '' </t> <t> minnesota bridge collapsed during rush hour wednesday . </t>

```

## Scores
I first remove `<t>, </t>` from the output then run rouge scoring wrt test target:
```
ROUGE-1 (F): 0.346364
ROUGE-2 (F): 0.143606
ROUGE-3 (F): 0.082605
ROUGE-L (F): 0.242816

```
