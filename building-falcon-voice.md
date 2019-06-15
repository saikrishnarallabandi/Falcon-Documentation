# Building Falcon Voice

## Building character model

#### Data preparation

```text
python3.5 $FALCONDIR/dataprep_addlspec.py etc/tdd .
python3.5 $FALCONDIR/dataprep_addmspec.py etc/tdd .
python3.5 $FALCONDIR/dataprep_addtext.py etc/tdd .

cat etc/tdd.test | cut -d' ' -f 1 > fnames.val
cat etc/tdd.train | cut -d' ' -f 1 > fnames.train

python3.5 $FALCONDIR/train_tacotronseqwise.py --data-root etc --checkpoint-dir checkpoints_seqwise
python3.5 synthesize_tacotronone.py checkpoints_seqwise/checkpoint_step110000.pth test.txt tts_seqwise
```

