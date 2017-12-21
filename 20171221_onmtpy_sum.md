## Trained model
* [epoch 3, ppl 15.27, acc 50.23](https://goo.gl/Qr9Wq5) [(zip) 104 Mo]

## Known Issues
* https://github.com/OpenNMT/OpenNMT-py/issues/457 
---

Using OpenNMT-py a13785d97c9f1adb3f0229b4210f6aba4a2562c7

## Preprocessing
The problem may come from the dataset which I processed again. I'm basically running:

1. [A. See's preprocessing](https://github.com/OpenNMT/cnn-dailymail) 
2. @mataney 's script to get text file from TF bins ([gist here](https://gist.github.com/mataney/67cfb05b0b84e88da3e0fe04fb80cfc8))
3. I'm then replacing `<s>`/`</s>` tokens from target files to `<t>`/`</t>`
4. OpenNMT preprocessing as follows:
```
  python preprocess.py \
      -train_src $data/train.src.txt \
      -train_tgt $data/train.tgt.repeos.txt \
      -valid_src $data/valid.src.txt \
      -valid_tgt $data/valid.tgt.repeos.txt \
      -save_data $root/data \
      -src_seq_length 10000 \
      -tgt_seq_length 10000 \
      -src_seq_length_trunc 400 \
      -tgt_seq_length_trunc 100 \
      -dynamic_dict \
      -share_vocab \
      -save_data $root/data 
```
(`*.repeos.txt` files are those with `</t>` replacing `</s>`)

## Training 
```
  python train.py -data $root/data \
        -save_model $root/model \
        -copy_attn \
        -global_attention mlp \
        -word_vec_size 128 \
        -rnn_size 256 \
        -layers 1 \
        -encoder_type "brnn" \
        -epochs 16 \
        -seed 777 \
        -batch_size 32 \
        -max_grad_norm 2 \
        -share_decoder_embeddings \
        -gpuid 0
```

**Epoch 3:**
```
Epoch  3,  8950/ 8973; acc:  48.18; ppl:  18.85; 7984 src tok/s; 1285 tgt tok/s;  13659 s elapsed
Train perplexity: 19.9812
Train accuracy: 47.5108
Validation perplexity: 15.2682
Validation accuracy: 50.2284
```


## Translation 
```
  best_model=$(ls -lsrt $root/model*.pt | tail -n 1 | awk '{print $NF}')
  python translate.py -model "$best_model" \
                      -src $data/test.src.txt \
                      -gpu "$gpu" \
                      -batch_size 1 \ 
                      -verbose \
                      -n_best 5 \
                      -beam_size 5
```
