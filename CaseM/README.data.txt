README:

The data files contain simulations of BH-BH mergers formed through the chemically homogeneous evolutionary channel as described in the following two papers:

Mandel & de Mink, 2016, MNRAS, http://mnras.oxfordjournals.org/content/early/2016/02/17/mnras.stw379 :  Description of the model assumptions and cosmological merger rates
de Mink & Mandel, 2016, arXiv, http://www.arxiv.org/abs/1603.02291 :  Description of the simulations for the detection rate and comparison with GW150914

We make the full output of our simulations of binary black hole mergers through chemically homogeneous evolution available to the community, in order to allow further comparisons with current and future data and with simulations of other channels. These simulations are free to use; we ask only that that both papers above are cited when the data are used for publication.

Below we describe (1) the file names, (2) the file content and (3) an example of how to read these files with python/matlab. Please contact us for other formats. 

Selma de Mink & Ilya Mandel, March 7, 2016



#################
# 1 Files names #
################# 

We provide the output for various simulations (Xxxx = Default, PoorMixing, Zmin0.002, ...).  These refer to variations in the physical assumptions, see Table 1 Mandel & de Mink (2016) and de Mink & Mandel.

For each model, we provide three files with 
 
XxxxBinaries.mat                # All simulated merging binaries 
XxxxBinariesDetections.mat      # Binaries detectable at design aLIGO sensitivity
XxxxBinariesDetectionsO1.mat    # Binaries detectable at the sensitivity of the O1 science run

##################
# 2 File Content #
##################
	
The files contain several scalars, 1 and 2D arrays summarizing the parameters and output of the simulations. 

# Scalar Entries
	 
Key           = default # Description
--------------------------------------
N             = 1e+08	# Total number of stars drawn for Monte Carlo of fixed Metallicity (not varied)
MaxMass       = 300	# Maximum extent of initial mass function (solar masses)
Zmin          = 0.004 	# Adopted threshold metallicity for homogeneous evolution
Zsolar        = 0.0134 	# Adopted value for solar metallicity (mass fraction)
PISNmass      = 63	# adopted threshold for pair instability supernovae (Msun)
massloss      = 1	# Factor for varying mass loss assumption 
avecfactor    = 0	# Angular momentum loss parameter. Default is 0 (assuming fast winds). Otherwise multiply separation by this factor 
cosmodex      = 0 	# default is 0, otherwise this many dex of spread in the metallicity distribution
homogeneous   = 0       # default is 0 (standard fit to Yoon 2006), 1 is alternative fit reducing the window for homogeneous evolution (section 7.1 of Mandel & de Mink)
PSD           = “full”  # detector power spectral density used as input for sensitivity calculations (‘full’ for design sensitivity, ‘O1’ for O1 run sensitivity)


Mform         # Total mass of simulated star formation
Ninteresting  # Number of simulated BH-BH binary systems that merge within a Hubble time

# Simulated data for all simulated BH-BH merging binaries formed through the homogeneous channel that coalesce within a Hubble time. These are the sample binaries k.  [1D arrays of length Ninteresting (= 515 in default model)]:

Key			# Description
---------------------------------------------------------
m1vecInt 		# Initial mass primary star (Msun)
m2vecInt  		# Initial mass secondary star (Msun)
mfinal1vecInt  		# Final BH mass primary star (Msun)
mfinal2vecInt  		# Final BH mass secondary star (Msun)
Mcvec			# Chirp mass (Msun)
TdelayvecInt  		# Delay time, i.e. time between formation of the stars and coalescence of the BH-BH system  (seconds)
Rdetectionsbinary	# Detection rate per year per sample binary [


# Redshift dependent information [1D arrays of length 1001]:

Key                     # Description
--------------------------------------------------------------------
zvec 		# used redshift bins: 0, 0.01, 0.02, ... 10.0
SFRlowZ 	# Star formation rate at metallicity Z < Zmin  (Msun Mpc^-3 of comoving volume per year in the source frame)
dVc 		# Comoving volume in each redshift bin (Mpc^3) 
tL 		# Lookback time at a given redshift (years)
Dl 		# Luminosity Distance (light-seconds)

RdetectionsZformation  # Detection rate per year per redshift bin (formation redshift)
RdetectionsZmerger     # Detection rate per year per redshift bin (merger redshift)

## 2D array (Ninteresting x 1001) of the contribution of each simulated binary placed at each redshift to the number of detections 

Key                     # Description
---------------------------------------------------------
MergerRate 		# Number of mergers as a function of merger redshift (zvec) for each sample binary k (Mpc^-3 of comoving volume per year in the source frame)

Rdetections 	        # Detection rate per year by merger redshift per sample binary k


##  If reading in using python the following additional keys will be automatically generated. 

__globals__, __version__, __header__



###########################################################
# 3 Example of Reading the datafiles with Python / Matlab #
###########################################################


[Matlab Example]

As an illustrative example, here is the sequence of steps to find the 90%-confidence lower bound on the mass ratio q for detectable systems at full aLIGO sensitivity in the default model:

model='Default';
filenamedet=[model, 'BinariesDetections.mat'];
load(filenamedet);	%load all data

% compute the vector of mass ratios
qfinalvec=mfinal2vecInt./mfinal1vecInt; 
	
% sort q’s, keeping track of simulated binary indices
qs=sortrows([qfinalvec' [1:Ninteresting]']);

% weigh each simulated binary by its contribution to total detection rate; 
% compute a cumulative distribution function
qs(:,2)=cumsum(Rdetectionsbinary(qs(:,2))/sum(RdetectionsZmerger));

[blah,q10ind]=min(abs(qs(:,2)-0.1));
display(['q < ', num2str(qs(q10ind,1),2)])


[Python Example]

The python Scipy IO library can directly read the output files into a python dictionary using using the "loadmat" routine with the following two lines of code:

        import scipy.io
        data = scipy.io.loadmat('DefaultBinaries.mat')

After which the content can inspected in the regular way. For example
        print data.keys()  # will show all keys
        print data["Ninteresting"]  # will print number of BH-BH mergers found in the Monte carlo
        print data["mfinal1vecInt"] # will print all final BH mass for primary star of interesting systems
        print data["zvec"] # gives all redshift bins
        print data["SFRlowZ"] # gives the fractional star formation metallicity below the threshold for the redshift bins above

        print data["MergerRate"] # provides the contribution to the merger rate for all "Ninteresting" systems for each redshift bin in data["zvec"]


