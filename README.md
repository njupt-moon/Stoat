# Stoat Prototype
Stoat (STochastic model App Tester) is a guided approach to perform stochastic model-based testing on Android Apps. The idea is to thoroughly test the functionalities of an app from its GUI model, and validate the app’s behavior by enforcing various user/system interactions. 

# Publication
[1] Guided, Stochastic Model-based GUI Testing of Android Apps (FSE'2017)

```
@inproceedings{Stoat2017,
  author = {Su, Ting and Meng, Guozhu and Chen, Yuting and Wu, Ke and Yang, Weiming and Yao, Yao and Pu, Geguang and Liu, Yang and Su, Zhendong},
  title = {Guided, Stochastic Model-based GUI Testing of Android Apps},
  booktitle = {Proceedings of the 2017 11th Joint Meeting on Foundations of Software Engineering},
  series = {ESEC/FSE 2017},
  year = {2017},
  isbn = {978-1-4503-5105-8},
  location = {Paderborn, Germany},
  pages = {245--256},
  numpages = {12},
  publisher = {ACM},
  address = {New York, NY, USA},
  keywords = {GUI Testing, Mobile Apps, Model-based Testing},
} 
```

[2] FSMdroid: Guided GUI Testing of Android Apps (ICSE'16, ACM SRC)

```
@inproceedings{FSMdroid16,
  author    = {Ting Su},
  title     = {FSMdroid: guided {GUI} testing of android apps},
  booktitle = {Proceedings of the 38th International Conference on Software Engineering,
               {ICSE} 2016, Austin, TX, USA, May 14-22, 2016 - Companion Volume},
  pages     = {689--691},
  year      = {2016}
}
```

# Quick Review

Click this [link](https://tingsu.github.io/files/stoat.html) for quick review on the approach workflow, evaluation results, demo and etc.

# Setup 

You can checkout Stoat from this repo.

### Environment Configration
* Ruby: 2.1 

* [Nokogiri](http://www.nokogiri.org/tutorials/installing_nokogiri.html)

(If your default ruby version is lower than 2.1, the installation of Nokogiri will fail. In this case, please [upgrade ruby to 2.1 or higher](https://stackoverflow.com/questions/26595620/how-to-install-ruby-2-1-4-on-ubuntu-14-04). If the installation still fails due to [this issue](https://github.com/github/pages-gem/issues/133), you need to execute "sudo apt-get install ruby2.1-dev")

* Python: 2.7

* Android SDK: API 18/19

* Ubuntu 14.04/Linux

* uiautomator (A python wrapper of [UIAutomator](https://github.com/xiaocong/uiautomator))


We strongly recommend to run Stoat on a physical machine to ensure the performance instead of ruuning on virtual machines (e.g., VirtualBox or Docker). In addition, please choose x86 image if you use Android emulators, and [setup hardware acceleration](https://developer.android.com/studio/run/emulator-acceleration.html) for [Ubuntu](https://help.ubuntu.com/community/KVM/Installation).

Please export ANDROID_HOME (for android sdk), PYTHON_PATH (for uiautomator), CLASSPATH (for soot)

 Example:
  ```
  export ANDROID_HOME="/home/XX/Android/Sdk"
  export PYTHONPATH="/home/XX/uiautomator"
  export CLASSPATH="/home/XX/fsmdroid/soot-github/lib/soot-develop.jar
  export PATH=$PATH:${ANDROID_HOME}/build-tools/25.0.0:${ANDROID_HOME}/tools:${ANDROID_HOME}/platform-tools:
  ```
You may also need to modify "Stoat/CONF.txt" to set the tool path.
  
# Usage

Stoat provides several ways to test android apps by command lines. Note before running Stoat, please disable keyboard (for emulator, add "hw.keyboard=yes" in its config.ini; for real device, please install the "com.wparam.nullkeyboard_1.apk" at "Stoat/bin/sdcard", and configure it to be the default input method)

1. Ant opens-soruce projects

 	ruby run_stoat_testing.rb --app_dir /home/XX/caldwell.ben.bites_4_src --avd_name testAVD_1 --avd_port 5554 --stoat_port 2000 --project_type ant 
 	
2. apk without instrumentation

	**Note this may mitigate Stoat's power due to lack of coverage info for test optimization. Otherwise, you need to instrument apk with Ella**
    
	ruby run_stoat_testing.rb --app_dir /home/XX/Bites.apk --avd_name testAVD_1 --avd_port 5554 --stoat_port 2000 (the output will be under "/home/XX/Bites-output")
 	 
3. Use real device, ant projects

	Please open wifi, and disable keyboard before do testing on real device.
    
 	ruby run_stoat_testing.rb --app_dir /home/XX/caldwell.ben.bites_4_src/ --real_device_serial cf00b9e6 --stoat_port 2000 --project_type ant 
	
4. A list of apps (If they are apks, append the option "--project_type apk")

	ruby run_stoat_testing.rb --apps_dir /home/XX/test_apps/ --apps_list /home/XX/test_apps/apps_list.txt --avd_name testAVD_1 --avd_port 5554 --stoat_port 2000 --force_restart 


### Subject Requirement:
* instrumented apps (use [Emma](http://emma.sourceforge.net/index.html) for open-source apps and [Ella](https://github.com/saswatanand/ella) for closed-source apps) should end with "-debug.apk"
* closed-source/non-instrumented apk can also be tested, and its name should end with ".apk" 


# Output

stoat_fsm_building_output: the outputs of model construction. 

	
	 crashes/ -- crash report (include crash stack, event trace, screen shots); 
	 ui/ -- ui xml files; 
	 coverage/ -- coverage files during model construction; 
	 FSM.txt/app.gv -- xdot model graph; 
	 fsm_building_process.txt/fsm_states_edges.txt -- the model building process, mainly the increasing coverage/#states/#edges 
	 CONF.txt -- configuration file 
         
	 
stoat_mcmc_sampling_output: the outputs of mcmc sampling. 
    
    	
	 crashes/ -- crash report (include crash stack, event trace, screen shots); 
	 MCMC_coverage/ -- the coverage data during mcmc sampling; 
	 mcmc_sampling_progress.txt/mcmc_data.txt -- mcmc sampling progress data; 
	 initial_markov_model.txt/optimal_markov_model.txt/mcmc_models.txt -- the initial/optimal/all mcmc sampling models; 
	 mcmc_all_history_testsuites.txt -- all executed test suites for mcmc sampling; 
	 test_suite_to_execute.txt -- the current test suite under execution;
	 CONF.txt -- configuration file. 
	 
     
coverage: the all coverage data during two phases

## Benchmark

Some benchmark apps used in our paper.

##  Notes
* This implementation has been tested with Android 4.4, running on Ubuntu 14.04. 
* If measure statement coverage for open-sourced apps, the subjects need to be processed to support EMMA instrumentation:
(Please refer to [Dynodroid](https://code.google.com/archive/p/dyno-droid/) for details.) You can also refer to the apps in the benchmark.
* This version only supports testing ant projects.

## Contact
[Ting Su](http://tingsu.github.io/)
All Copyright Reserved.
