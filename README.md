# ARMED & AIMED & AIMED-RL
**************************

Welcome to ARMED & AIMED/RL (AxMED): Automatic Random/Intelligent Malware Modifications to Evade Detection 

AxMED was designed to understand how automatically injected perturbations to Windows portable executable (PE) malware impact static classifiers without affecting the sample's functionality and thus keeping the new malicious mutations valid. This work implements the action space and GBDT model proposed on the [OpenAI gym malware](https://github.com/endgameinc/gym-malware) environment. It has been originally implemented using Fedora 29/30 and tested on Ubuntu 16.

## Part 1: Installation instructions 

Download the ARMED/AIMED repository: 
```
$ git clone https://github.com/zRapha/AIMED 
```
Create a virtual environment & activate it: 
```
$ python3 -m venv axmed-env
$ source axmed-env/bin/activate
```
Install required packages: 
```
$ pip install -r requirements.txt 
```
## Part 2: Functionality test environment  
Per default it will be used a Cuckoo analysis environment that has an extensive documentation support: https://cuckoo.sh/docs/. Cuckoo provides dynamic analysis results, which can be useful to understand the adversarial examples generated. A local beta-test implementation is also provided to avoid using an external service. 

## Part 3: Detection environment  
A local classification model is implemented to perform detection using a pre-trained classifier. For those looking for more results, we provide the option of using agreggators via REST APIs in order to assess adversarial examples against a wider range of scanners. 

## Part 4: Dataset 
There are several public repositories containing labeled malicious samples to test the environment. Once the data is acquired, it should be placed under samples/unzipped/. 

## Further environment isolation [Optional] 
Even though the manipulations do not require to run any file, the functionality stage does. Hence, it is  recommended to use isolated sandboxes and simulated services. One option is to use inetsim. 

Disable interface: 
```
$ sudo ifconfig <network_int> down 
```

Run inetsim:
```
$ cd /etc/default/inetsim-1.2.8/ 
$ sudo ./inetsim 
```

Note that automatically retrieving the detection rate for a malware sample from an online agreggator will no longer be functional unless adjusted or checked manually.

## Part 5: How to run AxMED

1. Activate Cuckoo Python venv: 
```
$ source ~/Documents/venvs/cuckoo-env/bin/activate
```

2. Run Mongo DB for webserver: 
```
$ sudo service mongod start 
```

3. Run webserver [optional]: 
```
$ cd ~/.cuckoo/ 
$ cuckoo web 
```

4. Run API & Cuckoo sandbox: 
```
$ cuckoo api 
$ cuckoo
```
5. Run AxMED to find m adversarial examples by injecting p perturbations: 
```
$ ./axmed.py -p 5 -m 1
```

Observation: Note that an early working version of the AIMED-RL module has been provided along with the paper publication. However, a new and more adjusted release is expected around Q12022. 

## Part 6: How to run AIMED-RL
### Setup
0. Create a new virtual (conda) environment and install the requirements from requirements.txt
1. Make sure *upx* is installed on your system (tested: 3.91, but more recent versions should be fine, too)

2. Please ensure that the main function in axmed.py looks like this:
```python
if __name__ == '__main__':
    scanner = 'GradientBoosting'
    main('AIMED-RL', scanner)
```

3. Configure the PARAM_DICT in the *rl.py* file with the options you want AIMED-RL to use.

### Train/Evaluate
1. Make sure to split your set of malware files in at least two separate sets: A training set (should be located in 
*samples/unzipped* for example, `PARAM_DICT["malware_path"]`) and an evaluation set (should be located in *samples/evaluate_set*).
*The evaluation data must never be included in the training data!*

2. Call axmed.py with --train and/or --eval to start the AIMED-RL workflow
```
# Train only:
python3 axmed.py --train 
# Train and eval:
python3 axmed.py --train --eval
# Only evaluate existing agent
python3 axmed.py -dir your-agent-directory --eval
# If it doesn't find the training report you may have to copy the corresponding training_report.csv manually to the 
agent's directory
```

For more information about AIMED-RL please refer to the [paper](https://link.springer.com/chapter/10.1007/978-3-030-86514-6_3),
to my blog [article](https://sebief.github.io/it-security/tutorial/aimedRL/) or do not hesitate to create an issue!

## Citation  

For AIMED-RL: 
```
@inproceedings{labaca-castro2019aimed-rl,
  title={AIMED-RL: Exploring Adversarial Malware Examples with Reinforcement Learning },
  author={Labaca-Castro, Raphael and Franz, Sebastian and Rodosek, Gabi Dreo},
  booktitle={European Conference on Machine Learning and Knowledge Discovery in Databases (ECML PKDD)},
  pages={1--x},
  year={2021},
  organization={Springer}
}
```

For AIMED: 
```
@inproceedings{labaca-castro2019aimed,
  title={AIMED: Evolving Malware with Genetic Programming to Evade Detection},
  author={Labaca-Castro, Raphael and Schmitt, Corinna and Rodosek, Gabi Dreo},
  booktitle={2019 18th IEEE International Conference On Trust, Security And Privacy In Computing And Communications/13th IEEE International Conference On Big Data Science And Engineering (TrustCom/BigDataSE)},
  pages={240--247},
  year={2019},
  organization={IEEE}
}
```

For ARMED: 
```
@inproceedings{labaca-castro2019armed,
  title={ARMED: How Automatic Malware Modifications Can Evade Static Detection?},
  author={Labaca-Castro, Raphael and Schmitt, Corinna and Rodosek, Gabi Dreo},
  booktitle={2019 5th International Conference on Information Management (ICIM)},
  pages={20--27},
  year={2019},
  organization={IEEE}
}
```

