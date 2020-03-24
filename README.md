# susy_llp_fullsim_2016
2016 Summer16 SUSY LLP Fullsim signal generation

# PPD RunII guideline
https://twiki.cern.ch/twiki/bin/viewauth/CMS/PdmVAnalysisSummaryTable

Take 2016 condition.

# step 0: LHE, GEN, SIM
```
export SCRAM_ARCH=slc6_amd64_gcc481
source /cvmfs/cms.cern.ch/cmsset_default.sh
scram p CMSSW CMSSW_7_1_24
cd CMSSW_7_1_24/src/
eval `scram runtime -sh`

mkdir -p Configuration/GenProduction/python/

scram b

seed=$(($(date +%s) % 100 + 1))

cmsDriver.py Configuration/GenProduction/python/Fullsim_TChiHH_fragment_LLP_ctau.py  --fileout file:Fullsim_TChiHH_200_pl1000_step0.root --mc --eventcontent RAWSIM,LHE --customise SLHCUpgradeSimulations/Configuration/postLS1Customs.customisePostLS1,Configuration/DataProcessing/Utils.addMonitoring --datatier GEN-SIM,LHE --conditions MCRUN2_71_V1::All --beamspot Realistic50ns13TeVCollision --step LHE,GEN,SIM --magField 38T_PostLS1 --python_filename Fullsim_TChiHH_200_pl1000_step0_cfg.py --no_exec --customise_commands process.RandomNumberGeneratorService.externalLHEProducer.initialSeed="int(${seed})" -n 10

cmsRun Fullsim_TChiHH_200_pl1000_step0_cfg.py
```
#step 1: DIGI
```
export SCRAM_ARCH=slc6_amd64_gcc530
source /cvmfs/cms.cern.ch/cmsset_default.sh
scram p CMSSW CMSSW_8_0_21
cd CMSSW_8_0_21/src
eval `scram runtime -sh`


scram b

cmsDriver.py step1 --fileout file:Fullsim_TChiHH_200_pl1000_step1.root --pileup_input dbs:/Neutrino_E-10_gun/RunIISpring15PrePremix-PUMoriond17_80X_mcRun2_asymptotic_2016_TrancheIV_v2-v2/GEN-SIM-DIGI-RAW  --mc --eventcontent PREMIXRAW --datatier GEN-SIM-RAW --conditions 80X_mcRun2_asymptotic_2016_TrancheIV_v8 --step DIGIPREMIX_S2,DATAMIX,L1,DIGI2RAW,HLT:@frozen2016 --nThreads 8  --datamix PreMix --era Run2_2016 --python_filename Fullsim_TChiHH_200_pl1000_step1_cfg.py --no_exec --customise Configuration/DataProcessing/Utils.addMonitoring -n 10

cp ../../CMSSW_7_1_24/src/Fullsim_TChiHH_200_pl1000_step0.root step1_SIM.root
cmsRun Fullsim_TChiHH_200_pl1000_step1_cfg.py
```
#Step 2: RECO --> AODSIM
```
cmsDriver.py step2 --filein file:Fullsim_TChiHH_200_pl1000_step1.root --fileout file:Fullsim_TChiHH_200_pl1000_step2.root --mc --eventcontent AODSIM --runUnscheduled --datatier AODSIM --conditions 80X_mcRun2_asymptotic_2016_TrancheIV_v8 --step RAW2DIGI,RECO,EI --nThreads 8 --era Run2_2016 --python_filename Fullsim_TChiHH_200_pl1000_step2_cfg.py --no_exec --customise Configuration/DataProcessing/Utils.addMonitoring -n 10

cmsRun Fullsim_TChiHH_200_pl1000_step2_cfg.py
```
