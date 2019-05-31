# Building a vanilla voice

```text
### To get to this branch
git checkout VIRAT

### Some docker fundamentals
## One can create account on dockerhub.com. On machine, logging in is easy. Type 'docker login' on the terminal. It will prompt for username and password.
# docker pull X -> This command pulls an image. In this case, X is the name of the image
## Once we have the image locally, we can instantiate a container that runs this image
# docker run X
## To run interatively, use the flag -it
# docker run -it X
## To update the changes made in interative session, we need to commit and push. Three steps: exit the container, find container id, commit container id to image. push the image. docker ps -a lists the details
# docker commit container_name image_name
# docker push image_name
## Most scripts that are memory intense need more shared memory. We can use argument --shm-size for that
# docker run -it --shm-size 10GB X
## We can specify a name to the container for easier access later
# docker run -it --name container_name --shm-size 10GB X
## We can mount our disks onto docker
# docker run -it -v /loc_on_hdd : /loc_on_container --name container_name --shm-size 10GB X

### Docker
docker pull  srallaba/falcon:TacotronOne
nvidia-docker run -it --shm-size 10GB -v /home1:/home1  srallaba/falcon:TacotronOne
# Check that FALCONDIR variable is set
echo $FALCONDIR
# Should output $FESTVOXDIR/src/falcon where FESTVOXDIR is the location of festvox. 


### Sample build
Navigate to /home/srallaba/projects/text2speech/voices/cmu_us_rms_arctic

### Prepare data
cat etc/txt.done.data | tr '(' ' ' | tr ')' ' ' | tr '"' ' ' > etc/tdd
mkdir -p data/dataprep_tacotron1
python3.5 $FALCONDIR/prepare_data.py etc/tdd data/dataprep_tacotron1 wav


### Train Baseline Model
python3.5 $FALCONDIR/train_tacotronone.py --data-root data/dataprep_tacotron1 --checkpoint-dir checkpoints > log_tacotronone

### Test Baseline Model
python3.5 $FALCONDIR/synthesize_tacotronone.py checkpoints/checkpoint_step70000.pth data/dataprep_tacotron1/test.txt test/tacotronone
```

### Building a vanilla character level model

We follow a three step procedure: Data preparation, Training and Testing

#### Data Preparation

The idea is to strip extra tokens from the file 'txt.done.data' and have just $fname ' ' $text. Then we can iterate through the files and extract linear and mel spectra. 

```text
### Prepare data
cat etc/txt.done.data | tr '(' ' ' | tr ')' ' ' | tr '"' ' ' > etc/tdd
python3.5 $FALCONDIR/prepare_data.py etc/tdd .
./bin/traintest etc/txt.done.data.tacotron
./bin/traintest etc/tdd 
```

#### Training

```text
python3.5 $FALCONDIR/train_tacotronone.py --data-root ./etc --checkpoint-dir /home1/srallaba/development/falcon_stuff/baseline_rms/checkpoints_baseline > log_tacotronone_baseline 2>&1&Test Synthesis
```

#### Testing

```text
python3.5 $FALCONDIR/synthesize_tacotronone.py /home1/srallaba/development/falcon_stuff/baseline_rms/checkpoints_baseline/checkpoint_step10000.pth etc/tdd.test tts_baseline    
```

#### Samples

samples can be found [here](http://tts.speech.cs.cmu.edu/rsk/development/falcon/kitchens/kitchen_tacotron/tts_baseline/)

### Building a vanilla phone level model

We follow a three step procedure: Data preparation, Training and Testing

#### Data Preparation

The idea is to use the file 'txt.phseq.data' and have just $fname ' ' $text. Then we can iterate through the files and extract linear and mel spectra. 

```text
python3.5 $FALCONDIR/prepare_data_phseq.py ehmm/etc/txt.phseq.data . > log_phdataprep 2>&1&
./bin/traintest etc/txt.done.data.tacotron.phseq
```

#### Training

```text
python3.5 train_tacotronone_phones.py --data-root ./etc --checkpoint-dir checkpoints_ljspeechbaseline_phones > log_tacotronone_phones 2>&1&
```

#### Testing

```text
python3.5 $FALCONDIR/synthesize_tacotronone_phones.py checkpoints_ljspeechbaseline_phones/checkpoint_step25000.pth etc/txt.done.data.tacotron.phseq.test tts_phones
```



## 

