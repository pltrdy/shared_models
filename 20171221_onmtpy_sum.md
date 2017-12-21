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

**Output:** (also showing prediction in token form)

```
Loading: exp_rush3/model_acc_50.23_ppl_15.27_e3.pt
Loading model parameters.
allhyps: [[[3], [14, 60, 26, 657, 1701, 26, 36, 97, 1650, 48, 178, 11, 5, 774, 430, 38, 16, 3], [14, 60, 26, 657, 17
01, 26, 36, 57, 84, 1654, 8575, 9, 21, 114, 1746, 20, 11, 347, 8326, 38, 16, 3], [14, 60, 26, 657, 1701, 26, 36, 57,
 84, 1654, 8575, 9, 21, 114, 1746, 20, 11, 347, 8326, 4, 16, 14, 60, 26, 36, 57, 84, 1654, 8575, 9, 21, 114, 1746, 2
0, 11, 347, 8326, 38, 16, 3], [14, 60, 26, 657, 1701, 26, 36, 57, 84, 1654, 8575, 9, 21, 114, 1746, 20, 11, 347, 832
6, 4, 16, 14, 60, 26, 36, 24, 18, 8, 532, 44, 32, 209, 8, 123, 1119, 7, 1032, 440, 24, 7, 5, 1097, 38, 16, 3]]]

SENT 1: marseille , france -lrb- cnn -rrb- the french prosecutor leading an investigation into the crash of <unk> fl
ight <unk> insisted wednesday that he was not aware of any video footage from on board the plane . marseille prosecu
tor brice robin told cnn that `` so far no videos were used in the crash investigation . '' he added , `` a person w
ho has such a video needs to immediately give it to the investigators . '' robin 's comments follow claims by two ma
gazines , german daily bild and french paris match , of a cell phone video showing the harrowing final seconds from 
on board <unk> flight <unk> as it crashed into the french alps . all 150 on board were killed . paris match and bild
 reported that the video was recovered from a phone at the wreckage site . the two publications described the suppos
ed video , but did not post it on their websites . the publications said that they watched the video , which was fou
nd by a source close to the investigation . `` one can hear cries of ` my god ' in several languages , '' paris matc
h reported . `` metallic banging can also be heard more than three times , perhaps of the pilot trying to open the c
ockpit door with a heavy object . towards the end , after a heavy shake , stronger than the others , the screaming i
ntensifies . then nothing . '' `` it is a very disturbing scene , '' said julian <unk> , editor-in-chief of bild onl
ine . an official with france 's accident investigation agency , the bea , said the agency is not aware of any such 
video . lt. col. jean-marc <unk> , a french <unk> spokesman in charge of communications on rescue efforts around the
 <unk> crash site , told cnn that the reports were `` completely wrong '' and `` unwarranted . '' cell phones have b
een collected at the site , he said , but that they `` had n't been exploited yet . '' <unk> said he believed the ce
ll phones would need to be sent to the criminal research institute in <unk> <unk> , near paris , in order to be anal
yzed by specialized technicians working hand-in-hand with investigators . but none of the cell phones found so far h
ave been sent to the institute , <unk> said . asked whether staff involved in the search could have leaked a memory 
card to the media , <unk> answered with a <unk> `` no . '' <unk> told `` erin burnett : outfront '' that he had watc
hed the video and stood by the report , saying bild and paris match are `` very confident '' that the clip is real .
 he noted that investigators only revealed they 'd recovered cell phones from the crash site after bild and paris ma
tch published their reports . `` that is something we did not know before . ... overall we can say many things of th
e investigation were n't revealed by the investigation at the beginning , '' he said . what was mental state of <unk
> co-pilot ? german airline lufthansa confirmed tuesday that co-pilot andreas <unk> had battled depression years bef
ore he took the controls of <unk> flight <unk> , which he 's accused of deliberately crashing last week in the frenc
h alps . <unk> told his lufthansa flight training school in 2009 that he had a `` previous episode of severe depress
ion , '' the airline said tuesday . email correspondence between <unk> and the school discovered in an internal inve
stigation , lufthansa said , included medical documents he submitted in connection with resuming his flight training
 . the announcement indicates that lufthansa , the parent company of <unk> , knew of <unk> 's battle with depression
 , allowed him to continue training and ultimately put him in the cockpit . lufthansa , whose ceo <unk> <unk> previo
usly said <unk> was 100 % fit to fly , described its statement tuesday as a `` swift and seamless clarification '' a
nd said it was sharing the information and documents -- including training and medical records -- with public prosec
utors . <unk> traveled to the crash site wednesday , where recovery teams have been working for the past week to rec
over human remains and plane debris scattered across a steep mountainside . he saw the crisis center set up in <unk>
 , laid a wreath in the village of le <unk> , closer to the crash site , where grieving families have left flowers a
t a simple stone memorial . <unk> told cnn late tuesday that no visible human remains were left at the site but reco
very teams would keep searching . french president francois hollande , speaking tuesday , said that it should be pos
sible to identify all the victims using dna analysis by the end of the week , sooner than authorities had previously
 suggested . in the meantime , the recovery of the victims ' personal belongings will start wednesday , <unk> said .
 among those personal belongings could be more cell phones belonging to the 144 passengers and six crew on board . c
heck out the latest from our correspondents . the details about <unk> 's correspondence with the flight school durin
g his training were among several developments as investigators continued to delve into what caused the crash and <u
nk> 's possible motive for downing the jet . a lufthansa spokesperson told cnn on tuesday that <unk> had a valid med
ical certificate , had passed all his examinations and `` held all the licenses required . '' earlier , a spokesman 
for the prosecutor 's office in dusseldorf , christoph <unk> , said medical records reveal <unk> suffered from suici
dal tendencies at some point before his aviation career and underwent psychotherapy before he got his pilot 's licen
se . <unk> emphasized there 's no evidence suggesting <unk> was suicidal or acting aggressively before the crash . i
nvestigators are looking into whether <unk> feared his medical condition would cause him to lose his pilot 's licens
e , a european government official briefed on the investigation told cnn on tuesday . while flying was `` a big part
 of his life , '' the source said , it 's only one theory being considered . another source , a law enforcement offi
cial briefed on the investigation , also told cnn that authorities believe the primary motive for <unk> to bring dow
n the plane was that he feared he would not be allowed to fly because of his medical problems . <unk> 's girlfriend 
told investigators he had seen an eye doctor and a <unk> , both of whom deemed him unfit to work recently and conclu
ded he had psychological issues , the european government official said . but no matter what details emerge about hi
s previous mental health struggles , there 's more to the story , said brian russell , a forensic psychologist . `` 
psychology can explain why somebody would turn rage inward on themselves about the fact that maybe they were n't goi
ng to keep doing their job and they 're upset about that and so they 're suicidal , '' he said . `` but there is no 
mental illness that explains why somebody then feels entitled to also take that rage and turn it outward on 149 othe
r people who had nothing to do with the person 's problems . '' <unk> crash compensation : what we know . who was th
e captain of <unk> flight <unk> ? cnn 's margot haddad reported from marseille and pamela brown from dusseldorf , wh
ile laura smith-spark wrote from london . cnn 's frederik pleitgen , pamela <unk> , antonia mortensen , <unk> <unk> 
and <unk> <unk> contributed to this report .
PRED 1: 
[-6.7109] 
[-14.8559] <t> new : french prosecutor : `` no videos were used in the crash investigation '' </t>
[-19.0257] <t> new : french prosecutor : `` one can hear cries of ` my god ' in several languages '' </t>
[-30.6045] <t> new : french prosecutor : `` one can hear cries of ` my god ' in several languages . </t> <t> new : `
` one can hear cries of ` my god ' in several languages '' </t>
[-31.3469] <t> new : french prosecutor : `` one can hear cries of ` my god ' in several languages . </t> <t> new : `
` it is a person who has such a video needs to immediately give it to the investigators '' </t>
PRED SCORE: -6.7109


BEST HYP:
allhyps: [[[3], [14, 3248, 739, 326, 50246, 50108, 93, 24, 13, 8, 361, 2065, 1744, 741, 4, 16, 14, 3248, 739, 326, 9
3, 24, 13, 8, 361, 2065, 1744, 741, 4, 16, 3], [14, 3248, 739, 326, 50246, 50108, 93, 24, 13, 8, 361, 2065, 1744, 74
1, 4, 16, 14, 3248, 739, 326, 93, 24, 13, 8, 361, 2065, 1744, 741, 4, 16, 14, 3248, 739, 326, 93, 24, 13, 8, 361, 20
65, 1744, 741, 4, 16, 3], [14, 3248, 739, 326, 50246, 50108, 93, 24, 13, 8, 361, 2065, 1744, 741, 4, 16, 14, 3248, 7
39, 326, 93, 24, 13, 8, 361, 2065, 1744, 741, 4, 16, 14, 3248, 739, 326, 93, 24, 18, 8, 361, 2065, 1744, 741, 4, 16,
 3], [14, 3248, 739, 326, 50246, 50108, 93, 24, 13, 8, 361, 2065, 1744, 741, 4, 16, 14, 3248, 739, 326, 93, 24, 13, 
8, 361, 2065, 1744, 741, 4, 16, 14, 5, 3248, 739, 326, 93, 24, 13, 8, 361, 2065, 1744, 741, 4, 16, 3]]]

SENT 2: -lrb- cnn -rrb- the palestinian authority officially became the <unk> member of the international criminal c
ourt on wednesday , a step that gives the court jurisdiction over alleged crimes in palestinian territories . the fo
rmal accession was marked with a ceremony at the hague , in the netherlands , where the court is based . the palesti
nians signed the icc 's founding rome statute in january , when they also accepted its jurisdiction over alleged cri
mes committed `` in the occupied palestinian territory , including east jerusalem , since june 13 , 2014 . '' later 
that month , the icc opened a preliminary examination into the situation in palestinian territories , paving the way
 for possible war crimes investigations against israelis . as members of the court , palestinians may be subject to 
<unk> as well . israel and the united states , neither of which is an icc member , opposed the palestinians ' effort
s to join the body . but palestinian foreign minister <unk> <unk> , speaking at wednesday 's ceremony , said it was 
a move toward greater justice . `` as palestine formally becomes a state party to the rome statute today , the world
 is also a step closer to ending a long era of impunity and injustice , '' he said , according to an icc news releas
e . `` indeed , today brings us closer to our shared goals of justice and peace . '' judge <unk> <unk> , a vice pres
ident of the icc , said <unk> to the treaty was just the first step for the palestinians . `` as the rome statute to
day enters into force for the state of palestine , palestine <unk> all the rights as well as responsibilities that c
ome with being a state party to the statute . these are substantive commitments , which can not be taken lightly , '
' she said . rights group human rights watch welcomed the development . `` governments seeking to <unk> palestine fo
r joining the icc should immediately end their pressure , and countries that support universal acceptance of the cou
rt 's treaty should speak out to welcome its membership , '' said <unk> <unk> , international justice counsel for th
e group . `` what 's objectionable is the attempts to undermine international justice , not palestine 's decision to
 join a treaty to which over 100 countries around the world are members . '' in january , when the preliminary icc e
xamination was opened , israeli prime minister benjamin netanyahu described it as an outrage , saying the court was 
<unk> its boundaries . the united states also said it `` strongly '' disagreed with the court 's decision . `` as we
 have said repeatedly , we do not believe that palestine is a state and therefore we do not believe that it is eligi
ble to join the icc , '' the state department said in a statement . it urged the warring sides to resolve their diff
erences through direct negotiations . `` we will continue to oppose actions against israel at the icc as counterprod
uctive to the cause of peace , '' it said . but the icc begs to differ with the definition of a state for its purpos
es and refers to the territories as `` palestine . '' while a preliminary examination is not a formal investigation 
, it allows the court to review evidence and determine whether to investigate suspects on both sides . prosecutor <u
nk> <unk> said her office would `` conduct its analysis in full independence and impartiality . '' the war between i
srael and hamas militants in gaza last summer left more than 2,000 people dead . the inquiry will include alleged wa
r crimes committed since june . the international criminal court was set up in 2002 to prosecute genocide , crimes a
gainst humanity and war crimes . cnn 's <unk> <unk> , kareem <unk> and faith <unk> contributed to this report .
PRED 2: 
[-7.1448] 
[-25.0265] <t> palestinian foreign minister riad al-malki says it was a move toward greater justice . </t> <t> pales
tinian foreign minister says it was a move toward greater justice . </t>
[-32.2477] <t> palestinian foreign minister riad al-malki says it was a move toward greater justice . </t> <t> pales
tinian foreign minister says it was a move toward greater justice . </t> <t> palestinian foreign minister says it wa
s a move toward greater justice . </t>
[-33.4254] <t> palestinian foreign minister riad al-malki says it was a move toward greater justice . </t> <t> pales
tinian foreign minister says it was a move toward greater justice . </t> <t> palestinian foreign minister says it is
 a move toward greater justice . </t>
[-33.6763] <t> palestinian foreign minister riad al-malki says it was a move toward greater justice . </t> <t> pales
tinian foreign minister says it was a move toward greater justice . </t> <t> the palestinian foreign minister says i
t was a move toward greater justice . </t>
PRED SCORE: -7.1448



BEST HYP:
allhyps: [[[3], [14, 27, 384, 50139, 68, 48, 4097, 141, 5, 99, 11, 457, 4, 16, 14, 27, 384, 50139, 68, 48, 4097, 141
, 5, 99, 11, 457, 4, 16, 3], [14, 27, 384, 50139, 68, 48, 4097, 141, 5, 99, 11, 457, 4, 16, 14, 27, 384, 50139, 68, 
48, 4097, 141, 5, 99, 11, 457, 4, 16, 14, 27, 384, 50139, 68, 48, 4097, 4, 16, 3], [14, 27, 384, 50139, 68, 48, 4097
, 141, 5, 99, 11, 457, 4, 16, 14, 27, 384, 50139, 68, 48, 4097, 141, 5, 99, 11, 457, 4, 16, 14, 27, 384, 50139, 68, 
48, 4097, 141, 5, 99, 11, 457, 4, 16, 3], [14, 27, 384, 50139, 68, 48, 4097, 141, 5, 99, 11, 457, 4, 16, 14, 27, 384
, 50139, 68, 48, 4097, 141, 5, 99, 11, 457, 4, 16, 14, 27, 384, 50139, 68, 48, 4097, 141, 5, 99, 4, 16, 3]]]

SENT 3: -lrb- cnn -rrb- governments around the world are using the threat of terrorism -- real or perceived -- to ad
vance executions , amnesty international alleges in its annual report on the death penalty . `` the dark trend of go
vernments using the death penalty in a futile attempt to tackle real or imaginary threats to state security and publ
ic safety was stark last year , '' said <unk> shetty , amnesty 's secretary general in a release . `` it is shameful
 that so many states around the world are essentially playing with people 's lives -- putting people to death for ` 
terrorism ' or to quell internal instability on the ill-conceived premise of deterrence . '' the report , `` death s
entences and executions 2014 , '' cites the example of pakistan lifting a six-year moratorium on the execution of ci
vilians following the horrific attack on a school in peshawar in december . china is also mentioned , as having used
 the death penalty as a tool in its `` strike hard '' campaign against terrorism in the restive <unk> province of xi
njiang . the annual report <unk> the use of <unk> killing as a punitive measure across the globe , and this year 's 
edition contains some mixed findings . on one hand , the number of executions worldwide has gone down by almost 22 %
 on the previous year . at least <unk> people were executed around the world in 2014 , compared to <unk> in 2013 . a
mnesty 's figures do not include statistics on executions carried out in china , where information on the practice i
s regarded as a state secret . belarus and vietnam , too , do not release data on death penalty cases . `` the long-
term trend is definitely positive -- we are seeing a decrease in the number of executions -lrb- worldwide -rrb- , ''
 audrey <unk> , amnesty 's director of global issues , told cnn . `` a number of countries are closer to abolition ,
 and there are some signs that some countries will be <unk> by 2015 . -lrb- there are -rrb- signals of a world that 
is nearing abolition . '' while the report notes some encouraging signs , it also highlights a marked increase in th
e number of people sentenced to death in 2014 . at least <unk> people globally are confirmed to have been handed the
 sentence last year , an increase of 28 % compared with 2013 . the report notes that the spike in sentencing is attr
ibutable to <unk> in countries including egypt and nigeria , `` against scores of people in some cases . '' the orga
nization found `` positive developments '' worldwide , with most regions seeming to show reductions in the number of
 executions . opinion : sharp spike in death sentences . sub-saharan africa , for example , saw a 28 % fall in repor
ted cases , and executions recorded in the middle east and north africa were down 23 % compared to 2013 . `` even th
ough we 've highlighted some of the negative developments ... i think we would always highlight that there are posit
ive developments , '' <unk> said . `` across the board , with the exception of europe and central asia there were fe
wer reports of executions in every region . '' the resumption of the use of capital punishment in belarus -- the onl
y country in europe and central asia to execute people -- after a two year hiatus spoiled an <unk> decrease in count
ries using the death penalty by region . the united states has the dubious distinction of being the only country in 
the americas to conduct executions , but the number of convicts put to death here fell slightly , from 39 in 2013 to
 35 in 2014 . the state of washington also imposed a moratorium on executions last year . the u.s. remains one of th
e worst offenders for imposing capital punishment , with only iran -lrb- 289 + -rrb- , iraq -lrb- 61 + -rrb- , and s
audi arabia -lrb- 90 + -rrb- executing more people in 2014 . while figures are not available , amnesty estimates tha
t china also executes `` thousands '' of prisoners each year , `` more than the rest of the world put together . '' 
the report also highlights the imperfections in the judiciary processes that lead to many sentenced to death . `` in
 the majority of countries where people were sentenced to death or executed , the death penalty was imposed after pr
oceedings that did not meet international fair trial standards , '' the report stated . `` in 2014 amnesty internati
onal raised particular concerns in relation to court proceedings in afghanistan , bangladesh , china , egypt , iran 
, iraq , north korea , pakistan , saudi arabia and sri lanka . '' the united nations secretary-general , ban ki-moon
 , last year stressed the need to move toward abolition of capital punishment . `` the taking of life is too irrever
sible for one human being to inflict it on another , '' he said , in marking world day against death penalty in octo
ber . `` we must continue to argue strongly that the death penalty is unjust and incompatible with fundamental human
 rights . '' amnesty estimates that at least <unk> people were believed to be on death row at the end of 2014 .
PRED 3: 
[-6.9888] 
[-18.7648] <t> at least 607 people were executed around the world in 2014 . </t> <t> at least 607 people were execut
ed around the world in 2014 . </t>
[-23.8972] <t> at least 607 people were executed around the world in 2014 . </t> <t> at least 607 people were execut
ed around the world in 2014 . </t> <t> at least 607 people were executed . </t>
[-25.1037] <t> at least 607 people were executed around the world in 2014 . </t> <t> at least 607 people were execut
ed around the world in 2014 . </t> <t> at least 607 people were executed around the world in 2014 . </t>
[-25.1305] <t> at least 607 people were executed around the world in 2014 . </t> <t> at least 607 people were execut
ed around the world in 2014 . </t> <t> at least 607 people were executed around the world . </t>
PRED SCORE: -6.9888

```
```
