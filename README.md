HiggsAnalysis-CombinedLimit
===========================

### Official documentation

[Manual to run combine](https://twiki.cern.ch/twiki/bin/view/CMS/SWGuideHiggsAnalysisCombinedLimit#How_to_run_the_tool)

### Standalone compilation in `lxplus`
```
git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit
source env_standalone.sh 
make -j 8; make # second make fixes compilation error of first
```

### Stealth Stop Group Setup `(LPC)`
```
bash
source /cvmfs/cms.cern.ch/cmsset_default.sh 
export SCRAM_ARCH=slc6_amd64_gcc530
cmsrel CMSSW_8_1_0
cd CMSSW_8_1_0/src
cmsenv
git clone git@github.com:StealthStop/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
scram b clean
scram b -j8
```

Add CombineTools from CombineHarvester to your work area:
 (this is needed for impact plots, and possible crab submission)
```
From a CMSSW_8_1_0 release:
cd $CMSSW_BASE/src 
curl https://raw.githubusercontent.com/cms-analysis/CombineHarvester/master/CombineTools/scripts/sparse-checkout-https.sh > sparse-checkout-https.sh
bash sparse-checkout-https.sh
scram b
```

### Example

To combine the datacards for 2016 and 2017:
```
combineCards.py Y16=Card2016.txt Y17=Card2017.txt > ComboCard.txt
```

To make a RooFit workspace that contains our PDF definitions and input histograms:

For 2016 RPV:
```
root -l -q 'make_MVA_8bin_ws.C("2016","Keras_V1.2.5_v2","RPV","350","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2016","Keras_V1.2.5_v2","RPV","450","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2016","Keras_V1.2.5_v2","RPV","550","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2016","Keras_V1.2.5_v2","RPV","650","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2016","Keras_V1.2.5_v2","RPV","750","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2016","Keras_V1.2.5_v2","RPV","850","pseudodata")'
```

For 2017 RPV:
```
root -l -q 'make_MVA_8bin_ws.C("2017","Keras_V3.0.1_v2","RPV","350","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2017","Keras_V3.0.1_v2","RPV","450","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2017","Keras_V3.0.1_v2","RPV","550","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2017","Keras_V3.0.1_v2","RPV","650","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2017","Keras_V3.0.1_v2","RPV","750","pseudodata")'
root -l -q 'make_MVA_8bin_ws.C("2017","Keras_V3.0.1_v2","RPV","850","pseudodata")'
```

Can substitute other input directories, models, and mass points,
The last arguement can be:  pseudodata, pseudodataS, or data
The workspace goes into a file called MVA_<year>_<model>_<mass>_ws.root

--------------------------------------------------

Convert the card file (and the PDFs and input histograms references therein) into a workspace:

For 2016 RPV:
```
text2workspace.py Card2016.txt -o ws_2016_RPV_350.root -m 350 --keyword-value MODEL=RPV
text2workspace.py Card2016.txt -o ws_2016_RPV_450.root -m 450 --keyword-value MODEL=RPV
text2workspace.py Card2016.txt -o ws_2016_RPV_550.root -m 550 --keyword-value MODEL=RPV
text2workspace.py Card2016.txt -o ws_2016_RPV_650.root -m 650 --keyword-value MODEL=RPV
text2workspace.py Card2016.txt -o ws_2016_RPV_750.root -m 750 --keyword-value MODEL=RPV
text2workspace.py Card2016.txt -o ws_2016_RPV_850.root -m 850 --keyword-value MODEL=RPV
```

For 2017 RPV:
```
text2workspace.py Card2017.txt -o ws_2017_RPV_350.root -m 350 --keyword-value MODEL=RPV
text2workspace.py Card2017.txt -o ws_2017_RPV_450.root -m 450 --keyword-value MODEL=RPV
text2workspace.py Card2017.txt -o ws_2017_RPV_550.root -m 550 --keyword-value MODEL=RPV
text2workspace.py Card2017.txt -o ws_2017_RPV_650.root -m 650 --keyword-value MODEL=RPV
text2workspace.py Card2017.txt -o ws_2017_RPV_750.root -m 750 --keyword-value MODEL=RPV
text2workspace.py Card2017.txt -o ws_2017_RPV_850.root -m 850 --keyword-value MODEL=RPV
```

For 2016 + 2017 RPV:
```
text2workspace.py CardCombo.txt -o ws_Combo_RPV_350.root -m 350 --keyword-value MODEL=RPV
text2workspace.py CardCombo.txt -o ws_Combo_RPV_450.root -m 450 --keyword-value MODEL=RPV
text2workspace.py CardCombo.txt -o ws_Combo_RPV_550.root -m 550 --keyword-value MODEL=RPV
text2workspace.py CardCombo.txt -o ws_Combo_RPV_650.root -m 650 --keyword-value MODEL=RPV
text2workspace.py CardCombo.txt -o ws_Combo_RPV_750.root -m 750 --keyword-value MODEL=RPV
text2workspace.py CardCombo.txt -o ws_Combo_RPV_850.root -m 850 --keyword-value MODEL=RPV
```

Can substitute other models.
The above command produces a file called ws_<year>_<model>_<mass>.root and will be
fed to combine in the following commands.

--------------------------------------------------

Calculate quick asymptotic limits:

For 2016 RPV:
```
combine -M AsymptoticLimits ws_2016_RPV_350.root -m 350 --keyword-value MODEL=RPV --verbose 2 -n 2016 > log_2016RPV350_Asymp.txt
combine -M AsymptoticLimits ws_2016_RPV_450.root -m 450 --keyword-value MODEL=RPV --verbose 2 -n 2016 > log_2016RPV450_Asymp.txt
combine -M AsymptoticLimits ws_2016_RPV_550.root -m 550 --keyword-value MODEL=RPV --verbose 2 -n 2016 > log_2016RPV550_Asymp.txt
combine -M AsymptoticLimits ws_2016_RPV_650.root -m 650 --keyword-value MODEL=RPV --verbose 2 -n 2016 > log_2016RPV650_Asymp.txt
combine -M AsymptoticLimits ws_2016_RPV_750.root -m 750 --keyword-value MODEL=RPV --verbose 2 -n 2016 > log_2016RPV750_Asymp.txt
combine -M AsymptoticLimits ws_2016_RPV_850.root -m 850 --keyword-value MODEL=RPV --verbose 2 -n 2016 > log_2016RPV850_Asymp.txt
```

For 2017 RPV:
```
combine -M AsymptoticLimits ws_2017_RPV_350.root -m 350 --keyword-value MODEL=RPV --verbose 2 -n 2017 > log_2017RPV350_Asymp.txt
combine -M AsymptoticLimits ws_2017_RPV_450.root -m 450 --keyword-value MODEL=RPV --verbose 2 -n 2017 > log_2017RPV450_Asymp.txt
combine -M AsymptoticLimits ws_2017_RPV_550.root -m 550 --keyword-value MODEL=RPV --verbose 2 -n 2017 > log_2017RPV550_Asymp.txt
combine -M AsymptoticLimits ws_2017_RPV_650.root -m 650 --keyword-value MODEL=RPV --verbose 2 -n 2017 > log_2017RPV650_Asymp.txt
combine -M AsymptoticLimits ws_2017_RPV_750.root -m 750 --keyword-value MODEL=RPV --verbose 2 -n 2017 > log_2017RPV750_Asymp.txt
combine -M AsymptoticLimits ws_2017_RPV_850.root -m 850 --keyword-value MODEL=RPV --verbose 2 -n 2017 > log_2017RPV850_Asymp.txt
```

Outputs files with names such as:
higgsCombine2017.AsymptoticLimits.mH550.MODELRPV.root
which contains the expected (and observed) limits,
and a log file with a name such as log_2017RPV650_Asymp.txt

--------------------------------------------------

To make a limit plot:

Collect the above asymptotic limit root files into a results directory, such as fit_results_v5_Jan17_2019

```
root -l -q 'makePlots.C+("Jan17_2019","fit_results_v5_Jan17_2019","2017","RPV")'
```
(the first arguement above is just intended to be today's date, or other tag)

--------------------------------------------------


Calculate the significance using the asimov dataset and expected signal strength of 1.
Significance is printed to screen and is available in the file
higgsCombineTest.Significance.mH550.MODELRPV.root
```
combine -M Significance ws_2017_RPV_550.root -t -1 --expectSignal=1 -m 550 --keyword-value MODEL=RPV -n 2017RPV550_SignifExp
```

Calculate the observed significance:
```
combine -M ProfileLikelihood ws_2017_RPV_550.root --significance -m 550 --keyword-value MODEL=RPV -n 2017RPV550_SignifObs
```

--------------------------------------------------

Run the full fitDiagnostics:

```
combine -M FitDiagnostics ws_2017_RPV_550.root --plots --saveShapes --saveNormalizations -m 550 --keyword-value MODEL=RPV -n 2017RPV550 > log_2017RPV550.txt
```

The above command produces a root file called fitDiagnostics2017RPV550.root that contains RooPlots and RooFitResults.
It also produces a file called higgsCombine2017RPV550.FitDiagnostics.mH550.MODELRPV.root that has the signal strength.
It also produces a log file called log_2017RPV550.txt (yields and best fit signal strength are at the end of this log file).

Produce formatted plots of fit results for each MVA bin:
```
root -q -l fit_report_ESM.C("fitDiagnostics2017RPV550.root")
```
or do the same but without yellow fit uncertainty:
```
root -q -l fit_report_ESM.C("fitDiagnostics2017RPV550.root",false)
```

Print the parameters resulting from the fit:
```
python test/diffNuisances.py --all --abs fitDiagnostics2017RPV550.root
```

--------------------------------------------------

Study the impacts:
  (you need CombineTools from CombineHarvester for this step to work)

Fit for each POI:
```
../../CombineHarvester/CombineTools/scripts/combineTool.py -M Impacts -d ws_2017_RPV_550.root -m 550 --doInitialFit --robustFit 1
```

Scan for each nuisance parameter.  This does MultiDimFit --algo impact for each nuisance parameter
```
../../CombineHarvester/CombineTools/scripts/combineTool.py -M Impacts -d ws_2017_RPV_550.root -m 550 --doFits --parallel 4
```

Collect output and write to json file:
```
../../CombineHarvester/CombineTools/scripts/combineTool.py -M Impacts -d ws_2017_RPV_550.root -m 550 -o impacts.json
```

Plot the impacts
```
../../CombineHarvester/CombineTools/scripts/plotImpacts.py -i impacts.json -o impacts
```

--------------------------------------------------

Background-only fits to tt background, before and after systematic variations:
```
root -l
.x make_BkgOnly_ws.C("_btgUp");
combine -M FitDiagnostics RPV_550_BkgOnlyCard.txt --plots --saveShapes --saveNormalizations -n _btgUp
root -l fitDiagnostics_btgUp.root
shapes_fit_b->cd();
D1->cd();
TT->Draw();
etc.
```


### Running Fits on condor

Running all of the fits using condor

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit/condor
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t data        --output Fit_Data_2016
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t pseudodata  --output Fit_pseudoData_2016
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t pseudodataS --output Fit_pseudoDataS_2016

python condorSubmit.py --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y 2017 -t data        --output Fit_Data_2017
python condorSubmit.py --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y 2017 -t pseudodata  --output Fit_pseudoData_2017
python condorSubmit.py --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y 2017 -t pseudodataS --output Fit_pseudoDataS_2017

python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y Combo -t data        --output Fit_Data_Combo
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y Combo -t pseudodata  --output Fit_pseudoData_Combo
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y Combo -t pseudodataS --output Fit_pseudoDataS_Combo

```

Making limit plots from condor output

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
root -l -q 'makePlots.C("<date>_data2016","condor/Fit_Data_2016/output-files","2016","RPV")'
root -l -q 'makePlots.C("<date>_data2016","condor/Fit_Data_2016/output-files","2016","SYY")'
root -l -q 'makePlots.C("<date>_data2016","condor/Fit_Data_2016/output-files","2016","SHH")'

root -l -q 'makePlots.C("<date>_pseudodata2016","condor/Fit_pseudoData_2016/output-files","2016","RPV")'
root -l -q 'makePlots.C("<date>_pseudodata2016","condor/Fit_pseudoData_2016/output-files","2016","SYY")'
root -l -q 'makePlots.C("<date>_pseudodata2016","condor/Fit_pseudoData_2016/output-files","2016","SHH")'

root -l -q 'makePlots.C("<date>_data2017","condor/Fit_Data_2017/output-files","2017","RPV")'
root -l -q 'makePlots.C("<date>_data2017","condor/Fit_Data_2017/output-files","2017","SYY")'
root -l -q 'makePlots.C("<date>_data2017","condor/Fit_Data_2017/output-files","2017","SHH")'

root -l -q 'makePlots.C("<date>_pseudodata2017","condor/Fit_pseudoData_2017/output-files","2017","RPV")'
root -l -q 'makePlots.C("<date>_pseudodata2017","condor/Fit_pseudoData_2017/output-files","2017","SYY")'
root -l -q 'makePlots.C("<date>_pseudodata2017","condor/Fit_pseudoData_2017/output-files","2017","SHH")'
``` 

Making profile scan from condor output

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
python make_profile_plot.py --model RPV --year 2016 --data 0 --basedir condor --masses 350 450 550 650 750 850
python make_profile_plot.py --model SYY --year 2016 --data 0 --basedir condor --masses 350 450 550 650 750 850
python make_profile_plot.py --model SHH --year 2016 --data 0 --basedir condor --masses 350 450 550 650 750 850

python make_profile_plot.py --model RPV --year 2016 --data 1 --basedir condor --masses 350 450 550 650 750 850
python make_profile_plot.py --model SYY --year 2016 --data 1 --basedir condor --masses 350 450 550 650 750 850
python make_profile_plot.py --model SHH --year 2016 --data 1 --basedir condor --masses 350 450 550 650 750 850

python make_profile_plot.py --model RPV --year 2017 --data 0 --basedir condor --masses 300 350 400 450 500 550 600 650 700 750 800 850 900
python make_profile_plot.py --model SYY --year 2017 --data 0 --basedir condor --masses 300 350 400 450 500 550 600 650 700 750 800 850 900
python make_profile_plot.py --model SHH --year 2017 --data 0 --basedir condor --masses 300 350 400 450 500 550 600 650 700 750 800 850 900

python make_profile_plot.py --model RPV --year 2017 --data 1 --basedir condor --masses 300 350 400 450 500 550 600 650 700 750 800 850 900
python make_profile_plot.py --model SYY --year 2017 --data 1 --basedir condor --masses 300 350 400 450 500 550 600 650 700 750 800 850 900
python make_profile_plot.py --model SHH --year 2017 --data 1 --basedir condor --masses 300 350 400 450 500 550 600 650 700 750 800 850 900
```

Making fit results plots from condor output

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
python make_fit_report_plot.py
```

### Running toys on condor

Similar to running the normal set of fits but add extra flags

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit/condor
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t data        --output Fit_Data_2016        --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t pseudodata  --output Fit_pseudoData_2016  --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t pseudodataS --output Fit_pseudoDataS_2016 --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500

python condorSubmit.py --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y 2017 -t data        --output Fit_Data_2017        --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500
python condorSubmit.py --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y 2017 -t pseudodata  --output Fit_pseudoData_2017  --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500
python condorSubmit.py --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y 2017 -t pseudodataS --output Fit_pseudoDataS_2017 --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500

python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y Combo -t data        --output Fit_Data_Combo        --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y Combo -t pseudodata  --output Fit_pseudoData_Combo  --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500
python condorSubmit.py --inPut_2016 Keras_V1.2.5_v2 --inPut_2017 Keras_V3.0.1_v2 -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y Combo -t pseudodataS --output Fit_pseudoDataS_Combo --toy --rMin 0.15 --rMax 0.3 --rStep 0.01 --jPerR 5 -T 500

```

Now hadd output of all of these toy jobs

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit/condor
python hadder.py -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t data -p Fit_Data_2016
python hadder.py -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t data -p Fit_pseudoData_2016
python hadder.py -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2016 -t data -p Fit_pseudoDatas_2016

python hadder.py -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2017 -t data -p Fit_Data_2016
python hadder.py -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2017 -t data -p Fit_pseudoData_2016
python hadder.py -d RPV,SYY,SHH -m 350,450,550,650,750,850 -y 2017 -t data -p Fit_pseudoDatas_2016

python hadder.py -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y Combo -t data -p Fit_Data_2016
python hadder.py -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y Combo -t data -p Fit_pseudoData_2016
python hadder.py -d RPV,SYY,SHH -m 300,350,400,450,500,550,600,650,700,750,800,850,900 -y Combo -t data -p Fit_pseudoDatas_2016

```

Now can make limit plots with toy output

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
root -l -q 'makePlots.C("<date>_data2016","condor/Fit_Data_2016/output-files","2016","RPV","HybridNew")'
root -l -q 'makePlots.C("<date>_data2016","condor/Fit_Data_2016/output-files","2016","SYY","HybridNew")'
root -l -q 'makePlots.C("<date>_data2016","condor/Fit_Data_2016/output-files","2016","SHH","HybridNew")'

root -l -q 'makePlots.C("<date>_pseudodata2016","condor/Fit_pseudoData_2016/output-files","2016","RPV","HybridNew")'
root -l -q 'makePlots.C("<date>_pseudodata2016","condor/Fit_pseudoData_2016/output-files","2016","SYY","HybridNew")'
root -l -q 'makePlots.C("<date>_pseudodata2016","condor/Fit_pseudoData_2016/output-files","2016","SHH","HybridNew")'

root -l -q 'makePlots.C("<date>_data2017","condor/Fit_Data_2017/output-files","2017","RPV","HybridNew")'
root -l -q 'makePlots.C("<date>_data2017","condor/Fit_Data_2017/output-files","2017","SYY","HybridNew")'
root -l -q 'makePlots.C("<date>_data2017","condor/Fit_Data_2017/output-files","2017","SHH","HybridNew")'

root -l -q 'makePlots.C("<date>_pseudodata2017","condor/Fit_pseudoData_2017/output-files","2017","RPV","HybridNew")'
root -l -q 'makePlots.C("<date>_pseudodata2017","condor/Fit_pseudoData_2017/output-files","2017","SYY","HybridNew")'
root -l -q 'makePlots.C("<date>_pseudodata2017","condor/Fit_pseudoData_2017/output-files","2017","SHH","HybridNew")'

```
