# Make a tab delimited file
```
~/tabix-0.2.6/bgzip XLXVXGXM_merged_sorted.bam.vcf.gz
~/tabix-0.2.6/tabix -f -p vcf XLXVXGXM_merged_sorted.bam.vcf.gz
zcat XLXVXGXM_merged_sorted.bam.vcf.gz | /usr/local/vcftools/src/perl/vcf-to-tab > XLXVXGXM_merged_sorted.bam.vcf.gz.tab
```

# Running the ABBABABA test

Here is the script (Performs_ABBA_BABA_on_populations_diploid_outgroup.pl):
```
#!/usr/bin/env perl
use strict;
use warnings;
use lib qw(~/perl_modules);
use List::MoreUtils qw/ uniq /;

#  This program reads in a tab delimited genotype file generated
#  by the perl program "Gets_outgroup_sequence_from_axt_files.pl"
#  or from vcftools
#  and performs the population ABBA-BABA test using one outgroup
#  sequence and one or more individual sequences from three other
#  taxa (H3, H2, and H1). 

#  This analysis will include all positions that have data from at least 
#  one individual from species H3, H2, and H1, including those that that 
#  missing data in some individuals.

# I am adding the additional functionality that a diploid outgroup can be used.  This might get complicated
# and maybe is easiest to handle by selecting a random variant 

# to execute type Performs_ABBA_BABA_on_populations.pl inputfile.tab 1111100110000111100011100110010100000000 
# 3_6_14-18-19-20_2-3-4-5-6-7_32-33-34-35-36-37-38-39-40 output1.txt output2.txt
# where 1111100110000111100011100110010100000000 refers to whether or not each individual in the ingroup 
# in the vcf file is (1) or is not (0) female ,and 3_6 refers to (i) the column that contains the 
# outgroup nucleotide (3 in this case for rhesus) and (ii) the column number of the first individual in the ingroup 
# (6 in this case), and 14-18-19-20, 2-3-4-5-6-7, and 32-33-34-35-36-37-38-39-40 refer to H3, H1, and H2 samples as 
# itemized below (here, they are Borneo nemestrina, hecki, and tonkeana, respectively). 

# output1.txt is the one that is fed into the jacknife.R script
# output2.txt has more information for each window, including BBAA sites, fdom and dxy

# IMPORTANT: (i) and (ii) are columns beginning with 1 but (iii) is based on the individual samples such
# as enumerated below


#	1	BJE1488_sorted.bam		vict	
#	2	BJE1489_sorted.bam		vict	
#	3	BJE261_sorted.bam		vict	
#	4	BJE263_sorted.bam		vict	
#	5	BJE264_sorted.bam		vict	
#	6	BJE265_sorted.bam		vict	
#	7	BJE266_sorted.bam		vict	
#	8	BJE267_sorted.bam		vict	
#	9	BJE3545_sorted.bam		laevis	Niewoudtville, purple clade
#	10	BJE3639_sorted.bam		laevis	Beaufort West, Green clade
#	11	XG12_07_sorted.bam		gilli	CoGH
#	12	XG153_sorted.bam		gilli	CoGH
#	13	XG92_sorted.bam		gilli	CoGH
#	14	XGL713_123_sorted.bam		gilli	kleinmond
#	15	XGL713_177_sorted.bam		gilli	kleinmond
#	16	XGL713_179_sorted.bam		gilli	kleinmond
#	17	XGL713_180_sorted.bam		gilli	kleinmond
#	18	XGL713_181_sorted.bam		gilli	kleinmond
#	19	XGL713_232_sorted.bam		laevis	Yellow clade
#	20	XGUAE_124_sorted.bam		laevis	kleinmond
#	21	XGUAE_36_sorted.bam		gilli	CoGH
#	22	XGUAE_42_sorted.bam		gilli	CoGH
#	23	XGUAE_43_sorted.bam		gilli	CoGH
#	24	XGUAE_44_sorted.bam		gilli	CoGH
#	25	XGUAE_59_sorted.bam		laevis	CoGH
#	26	XGUAE_65_sorted.bam		laevis	CoGH
#	27	XGUAE_70_sorted.bam		laevis	kleinmond
#	28	XGUAE_71_sorted.bam		laevis	kleinmond
#	29	XGUAE_72_sorted.bam		laevis	kleinmond
#	30	XGUAE_92_sorted.bam		laevis	kleinmond
#	31	XGUAE_93_sorted.bam		laevis	kleinmond
#	32	XGUAE_97_sorted.bam		laevis	kleinmond
#	33	XL_CPT1_sorted.bam		laevis	CoGH
#	34	XL_CPT2_sorted.bam		laevis	CoGH
#	35	XL_CPT3_sorted.bam		laevis	CoGH
#	36	XL_CPT4_sorted.bam		laevis	CoGH
#	37	XLJONK_14_sorted.bam		laevis	Blue clade
#	38	XM_1_sorted.bam		muel	


# for example, with a tab file with the data above and XL as the reference, to use the muelleri sequence as the outgroup use the 
# number 41 for the outgroup because the number of individuals is 38, the muel seq is last, plus 3 reference columns.

# Cape vs Vic
# perl Performs_ABBA_BABA_on_populations_diploid_outgroup.pl XLXVXGXM_merged_sorted.bam.vcf.gz.tab 11111111111111111111111111111111111111 41_4_11-12-13-14-15-16-17-18-20-21-22-23-24_25-26-27-28-29-30-31-32-33-34-35-36_1-2-3-4-5-6-7-8 Omuel_H3_allgilli_H1_Capelaev_H2_vict.jk Omuel_H3_allgilli_H1_Capelaev_H2_vict.abbababa

# Cape vs other SA
# perl Performs_ABBA_BABA_on_populations_diploid_outgroup.pl XLXVXGXM_merged_sorted.bam.vcf.gz.tab 11111111111111111111111111111111111111 41_4_11-12-13-14-15-16-17-18-20-21-22-23-24_25-26-27-28-29-30-31-32-33-34-35-36_9-10-19 Omuel_H3_allgilli_H1_Capelaev_H2_SAlaev.jk Omuel_H3_allgilli_H1_Capelaev_H2_SAlaev.stats




my $inputfile = $ARGV[0];
my $input2 = $ARGV[1];
my $input3 = $ARGV[2];
my $outputfile = $ARGV[3];
my $outputfile2 = $ARGV[4];

unless (open DATAINPUT, $inputfile) {
	print "Can not find the input file.\n";
	exit;
}


my @temp;
my $range;
my $line_number=0;
my $counter=0;



unless (open(OUTFILE, ">$outputfile"))  {
	print "I can\'t write to $outputfile\n";
	exit;
}
print "Creating output file: $outputfile\n";

unless (open(OUTFILE2, ">$outputfile2"))  {
	print "I can\'t write to $outputfile2\n";
	exit;
}
print "Creating output file: $outputfile2\n";


my @sexes = split("",$ARGV[1]);
my @whotoinclude = split("_",$ARGV[2]);
my @H3=split("-",$whotoinclude[2]);
my @H1=split("-",$whotoinclude[3]);
my @H2=split("-",$whotoinclude[4]);

if($#whotoinclude != 4){
	print "Problem with number of taxa in commandline\n";
}



my @temp1;
my $previous= 0;
my $string;
my $m;
my $n;
my $w;
my $y;
my $x;
my @unique;
my $x_uniq;

my $number_of_H3_genotyped=($#H3 + 1);
my $number_of_H2_genotyped=($#H2 + 1);
my $number_of_H1_genotyped=($#H1 + 1);

my $number_of_individuals_genotyped=$#H3+$#H2+$#H1+3;

print "The number of individuals to assess is ",$number_of_individuals_genotyped,"\n";

my $number_of_female_individuals_genotyped;
for ($y = 0 ; $y <= $#H1 ; $y++ ) {
	if($sexes[$H1[$y]-1] == 1){
		$number_of_female_individuals_genotyped +=1;
	}	
}
for ($y = 0 ; $y <= $#H2 ; $y++ ) {
	if($sexes[$H2[$y]-1] == 1){
		$number_of_female_individuals_genotyped +=1;
	}	
}	
for ($y = 0 ; $y <= $#H3 ; $y++ ) {
	if($sexes[$H3[$y]-1] == 1){
		$number_of_female_individuals_genotyped +=1;
	}	
}
print "This includes ",$number_of_female_individuals_genotyped," female(s)\n";

my $sliding_window=5000000;
my $current_window=0;
my $window_counter=0;
my $current_chromosome="blah";


my %ABBA_hash;
my $ABBA_peak_hash;
my %BABA_hash;
my $BABA_peak_hash;
my %BBAA_hash;
my $A;
my $B;
my @allelez;
my $derived;
my $ancestral;
my $H3_derived_freq;
my $H1_derived_freq;
my $H2_derived_freq;
my $H3_ancestral_freq;
my $H1_ancestral_freq;
my $H2_ancestral_freq;
my $peak_H2_H3_derived_freq;
my $peak_H1_H3_derived_freq;
my @H3allelez;
my @H2allelez;
my @H1allelez;
my $diff;
my $num_comparisons;
my %H2_H3_pairwise_divergence_per_window;
my $diffH1H3;
my $num_comparisonsH1H3;
my %H1_H3_pairwise_divergence_per_window;
my %number_of_sites_per_window;
my %number_of_sites_per_windowH1H3;
my $ABBA_peak_hashH1H3;
my $BABA_peak_hashH1H3;
my %H1_pairwise_nucleotide_diversity_per_window;
my %H2_pairwise_nucleotide_diversity_per_window;
my %H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window;
my %H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window;


my $diffH2;
my $num_comparisonsH2;
my @H2_first_allelez;
my @H2_second_allelez;
my $aa;
my $bb;

my $diffH1;
my $num_comparisonsH1;
my @H1_first_allelez;
my @H1_second_allelez;

my %f_H2_H3;
my %f_H1_H3;
my %f_dm;
my %f_dm_counter;

my %f_H2_H3_counter;
my %f_H1_H3_counter;

my @diploidoutgroup;


while ( my $line = <DATAINPUT>) {
	chomp($line);
	@temp=split('\t',$line);
	if($temp[0] eq '#CHROM'){
		print "The outgroup sequence is ",$temp[$whotoinclude[0]-1],"\n";
		# here we need to randomly select a base from the outgroup genotype
		# but only if the outgroup genotype is a diploid genotype
		@diploidoutgroup=split('/',$temp[$whotoinclude[0]-1]);
		# pick a random allele
		$temp[$whotoinclude[0]-1] = $diploidoutgroup[rand @diploidoutgroup];		
		print " The randomly selected outgroup variant is ",$temp[$whotoinclude[0]-1],"\n";
		print "H3 population consists of these individuals: ";
			for ($y = 0 ; $y <= $#H3; $y++ ) {
				print $temp[$H3[$y]+$whotoinclude[1]-2]," ";
			}	
		print "\n";
		print "H1 population consists of these individuals: ";
			for ($y = 0 ; $y <= $#H1; $y++ ) {
				print $temp[$H1[$y]+$whotoinclude[1]-2]," ";
			}	
		print "\n";
		print "H2 population consists of these individuals: ";
			for ($y = 0 ; $y <= $#H2; $y++ ) {
				print $temp[$H2[$y]+$whotoinclude[1]-2]," ";
			}	
		print "\n";
	}
	else{	
		# here we need to randomly select a base from the outgroup genotype
		# but only if the outgroup genotype is a diploid genotype
		@diploidoutgroup=split('/',$temp[$whotoinclude[0]-1]);
		# pick a random allele
		$temp[$whotoinclude[0]-1] = $diploidoutgroup[rand @diploidoutgroup];		
		
		# This is a correction to deal with the way that the XL genome has chr9 and 10 annotated
		if($temp[0] eq "chr9_10L"){
			$temp[0] = "chr9and10L";
		}
		elsif($temp[0] eq "chr9_10S"){
			$temp[0] = "chr9and10S";
		}
		
		if($temp[0] ne $current_chromosome){
			$current_chromosome = $temp[0];
			$current_window = 0;
			$window_counter+=1;
		}
		until($temp[1] < ($current_window+$sliding_window)){
			$current_window = $current_window+$sliding_window;
			$window_counter+=1;
		}
		if(($temp[0] ne "chrX")&&($temp[0] ne "chrY")&&($temp[0] ne "chrM")){
			$string=();
			if((uc $temp[$whotoinclude[0]-1] eq "A")||(uc $temp[$whotoinclude[0]-1] eq "C")||(uc $temp[$whotoinclude[0]-1] eq "T")||(uc $temp[$whotoinclude[0]-1] eq "G")){
				# the outgroup is defined
				$string=();
				$A = uc $temp[$whotoinclude[0]-1];
				$string=$string.$A;
				# now calculate the frequency of the derived allele in the H3 data
				$H3_derived_freq=0;
				$H3_ancestral_freq=0;
				$derived=0;
				$ancestral=0;
				for ($y = 0 ; $y <= $#H3; $y++ ) {
					if(($temp[$H3[$y]+$whotoinclude[1]-2] ne './.')&&($temp[$H3[$y]+$whotoinclude[1]-2] ne 'N/N')&&($temp[$H3[$y]+$whotoinclude[1]-2] ne 'N/*')
						&&($temp[$H3[$y]+$whotoinclude[1]-2] ne 'A/*')&&($temp[$H3[$y]+$whotoinclude[1]-2] ne 'T/*')&&($temp[$H3[$y]+$whotoinclude[1]-2] ne 'G/*')&&($temp[$H3[$y]+$whotoinclude[1]-2] ne 'C/*')
						&&(length($temp[$H3[$y]+$whotoinclude[1]-2]) == 3)){
						@allelez=split('/',$temp[$H3[$y]+$whotoinclude[1]-2]);
						if($allelez[0] eq $A){
							$ancestral+=1;
						}
						else{
							$derived+=1;
						}
						$string=$string.$allelez[0];
						if($allelez[1] eq $A){
							$ancestral+=1;
						}
						else{
							$derived+=1;
						}
						$string=$string.$allelez[1];
					}	
				}
				if(($derived+$ancestral)>0){
					$H3_derived_freq=$derived/($derived+$ancestral);
					$H3_ancestral_freq=$ancestral/($derived+$ancestral);
				}
				# now calculate the frequency of the derived allele in the H1 data
				$H1_derived_freq=0;
				$H1_ancestral_freq=0;
				$derived=0;
				$ancestral=0;
				for ($y = 0 ; $y <= $#H1; $y++ ) {
					if(($temp[$H1[$y]+$whotoinclude[1]-2] ne './.')&&($temp[$H1[$y]+$whotoinclude[1]-2] ne 'N/N')&&($temp[$H1[$y]+$whotoinclude[1]-2] ne 'N/*')
						&&($temp[$H1[$y]+$whotoinclude[1]-2] ne 'A/*')&&($temp[$H1[$y]+$whotoinclude[1]-2] ne 'C/*')&&($temp[$H1[$y]+$whotoinclude[1]-2] ne 'G/*')&&($temp[$H1[$y]+$whotoinclude[1]-2] ne 'T/*')
						&&(length($temp[$H1[$y]+$whotoinclude[1]-2]) == 3)){
						@allelez=split('/',$temp[$H1[$y]+$whotoinclude[1]-2]);
						if($allelez[0] eq $A){
							$ancestral+=1;
						}
						else{
							$derived+=1;
						}
						$string=$string.$allelez[0];
						if($allelez[1] eq $A){
							$ancestral+=1;
						}
						else{
							$derived+=1;
						}
						$string=$string.$allelez[1];
					}
				}
				if(($derived+$ancestral)>0){
					$H1_derived_freq=$derived/($derived+$ancestral);
					$H1_ancestral_freq=$ancestral/($derived+$ancestral);
				}
				# now calculate the frequency of the derived allele in the H2 data
				$H2_derived_freq=0;
				$H2_ancestral_freq=0;
				$derived=0;
				$ancestral=0;
				for ($y = 0 ; $y <= $#H2; $y++ ) {
					if(($temp[$H2[$y]+$whotoinclude[1]-2] ne './.')&&($temp[$H2[$y]+$whotoinclude[1]-2] ne 'N/N')&&($temp[$H2[$y]+$whotoinclude[1]-2] ne 'N/*')
						&&($temp[$H2[$y]+$whotoinclude[1]-2] ne 'A/*')&&($temp[$H2[$y]+$whotoinclude[1]-2] ne 'T/*')&&($temp[$H2[$y]+$whotoinclude[1]-2] ne 'C/*')&&($temp[$H2[$y]+$whotoinclude[1]-2] ne 'G/*')
						&&(length($temp[$H2[$y]+$whotoinclude[1]-2]) == 3)){
						@allelez=split('/',$temp[$H2[$y]+$whotoinclude[1]-2]);
						if($allelez[0] eq $A){
							$ancestral+=1;
						}
						else{
							$derived+=1;
						}
						$string=$string.$allelez[0];
						if($allelez[1] eq $A){
							$ancestral+=1;
						}
						else{
							$derived+=1;
						}
						$string=$string.$allelez[1];
					}	
				}
				if(($derived+$ancestral)>0){
					$H2_derived_freq=$derived/($derived+$ancestral);
					$H2_ancestral_freq=$ancestral/($derived+$ancestral);
				}
				# only consider sites for which there are data for H1, H2, and H3
				if((defined($string))&&(($H1_derived_freq>0)||($H1_ancestral_freq>0))&&(($H2_derived_freq>0)||($H2_ancestral_freq>0))&&(($H3_derived_freq>0)||($H3_ancestral_freq>0))){
					# Now calculate the average pairwise divergence between H2 and H3
					$diff=0;
					$num_comparisons=0;
					@H3allelez=();
					@H2allelez=();
					for ($y = 0 ; $y <= $#H3; $y++ ) {
						if(($temp[$H3[$y]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H3[$y]+$whotoinclude[1]-2]) == 3)){
							for ($w = 0 ; $w <= $#H2; $w++ ) {
								if(($temp[$H2[$w]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H2[$w]+$whotoinclude[1]-2]) == 3)){
									# there are data for H2 and H3
									@H3allelez=split('/',$temp[$H3[$y]+$whotoinclude[1]-2]);
									@H2allelez=split('/',$temp[$H2[$w]+$whotoinclude[1]-2]);
									foreach(@H3allelez){
										if($_ ne $H2allelez[0]){
											$diff+=1; 
										}
										if($_ ne $H2allelez[1]){
											$diff+=1;
										}
										$num_comparisons+=2;	
									}
								}
							}
						}					
					}
					# Now calculate the average pairwise divergence between H1 and H3
					$diffH1H3=0;
					$num_comparisonsH1H3=0;
					@H3allelez=();
					@H1allelez=();
					for ($y = 0 ; $y <= $#H3; $y++ ) {
						if(($temp[$H3[$y]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H3[$y]+$whotoinclude[1]-2]) == 3)){
							for ($w = 0 ; $w <= $#H1; $w++ ) {
								if(($temp[$H1[$w]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H1[$w]+$whotoinclude[1]-2]) == 3)){
									# there are data for H1 and H3
									@H3allelez=split('/',$temp[$H3[$y]+$whotoinclude[1]-2]);
									@H1allelez=split('/',$temp[$H1[$w]+$whotoinclude[1]-2]);
									foreach(@H3allelez){
										if($_ ne $H1allelez[0]){
											$diffH1H3+=1; 
										}
										if($_ ne $H1allelez[1]){
											$diffH1H3+=1;
										}
										$num_comparisonsH1H3+=2;	
									}
								}
							}
						}					
					}
					# now calculate the average pairwise divergence for H2 and H3
					if($num_comparisons>0){
						# this does not depend on the position being polymorphic
							$number_of_sites_per_window{$window_counter."_".$current_chromosome."_".$current_window}+=1;
							$H2_H3_pairwise_divergence_per_window{$window_counter."_".$current_chromosome."_".$current_window}+=($diff/$num_comparisons);
							# later we will standardize this by the number of sites per window
					}
					# now calculate the average pairwise divergence for H1 and H3
					if($num_comparisonsH1H3>0){
						# this does not depend on the position being polymorphic
							$number_of_sites_per_windowH1H3{$window_counter."_".$current_chromosome."_".$current_window}+=1;
							$H1_H3_pairwise_divergence_per_window{$window_counter."_".$current_chromosome."_".$current_window}+=($diffH1H3/$num_comparisonsH1H3);
							# later we will standardize this by the number of sites per window
					}
					# Now calculate the average pairwise nucleotide diversity within H2
					# this approach is justified here: http://binhe.org/2011/12/29/calculate-nucleotide-diversity-per-base-pair-summation-method/
					# but with a formula that is probably much quicker then the stuff below pi = (2j(n-j) / n(n-1) ), where j is the number of minor
					# alleles out of n alleles total.
					$diffH2=0;
					$num_comparisonsH2=0;
					@H2_first_allelez=();
					@H2_second_allelez=();
					for ($y = 0 ; $y < $#H2; $y++ ) {
						for ($w = ($y+1) ; $w <= $#H2; $w++ ) {
							if(($temp[$H2[$y]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H2[$y]+$whotoinclude[1]-2]) == 3) && ($temp[$H2[$w]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H2[$w]+$whotoinclude[1]-2]) == 3)){
								# there are data for both H2 alleles
								@H2_first_allelez=split('/',$temp[$H2[$y]+$whotoinclude[1]-2]);
								@H2_second_allelez=split('/',$temp[$H2[$w]+$whotoinclude[1]-2]);
								# combine the alleles into one array
								@H2_first_allelez = (@H2_first_allelez, @H2_second_allelez);
								# check that this array has 4 elements
								if($#H2_first_allelez != 3){
									print "Problem with number of alleles @H2_first_allelez @H2_second_allelez\n";
								}
								else{ # calculate the average pairwise diversity for this pair of genotypes
									for ($bb = 0 ; $bb < $#H2_first_allelez; $bb++) {
										for ($aa = ($bb+1); $aa <= $#H2_first_allelez; $aa++) {
											if($H2_first_allelez[$bb] ne $H2_first_allelez[$aa]){
												$diffH2+=1; 
											}
											$num_comparisonsH2+=1;
										}
									}		
								}
							}
						}					
					}
					# for some sites, there may be only one individual with a genotype
					if($num_comparisonsH2==0){
						for ($y = 0 ; $y <= $#H2; $y++ ) {
							if(($temp[$H2[$y]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H2[$y]+$whotoinclude[1]-2]) == 3)){
								@H2_first_allelez=split('/',$temp[$H2[$y]+$whotoinclude[1]-2]);
								if($H2_first_allelez[0] ne $H2_first_allelez[1]){
									$diffH2+=1;
								}
								$num_comparisonsH2+=1;	
							}
						}	
					}	
					# now tabulate the average pairwise nucleotide diversity within H2
					if($num_comparisonsH2>0){
							# this does not depend on the position being polymorphic
								$H2_pairwise_nucleotide_diversity_per_window{$window_counter."_".$current_chromosome."_".$current_window}+=($diffH2/$num_comparisonsH2);
								$H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$window_counter."_".$current_chromosome."_".$current_window}+=1;
								# later we will standardize this by the number of sites per window
					}
					# Now calculate the average pairwise nucleotide diversity within H1
					$diffH1=0;
					$num_comparisonsH1=0;
					@H1_first_allelez=();
					@H1_second_allelez=();
					for ($y = 0 ; $y < $#H1; $y++ ) {
						for ($w = ($y+1) ; $w <= $#H1; $w++ ) {
							if(($temp[$H1[$y]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H1[$y]+$whotoinclude[1]-2]) == 3) && ($temp[$H1[$w]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H1[$w]+$whotoinclude[1]-2]) == 3)){
								# there are data for both H1 alleles
								@H1_first_allelez=split('/',$temp[$H1[$y]+$whotoinclude[1]-2]);
								@H1_second_allelez=split('/',$temp[$H1[$w]+$whotoinclude[1]-2]);
								# combine the alleles into one array
								@H1_first_allelez = (@H1_first_allelez, @H1_second_allelez);
								# check that the array has 4 elements
								if($#H1_first_allelez != 3){
									print "Problem with number of alleles @H1_first_allelez @H1_second_allelez\n";
								}
								else{
									for ($bb = 0 ; $bb < $#H1_first_allelez; $bb++) {
										for ($aa = ($bb+1); $aa <= $#H1_first_allelez; $aa++) {
											if($H1_first_allelez[$bb] ne $H1_first_allelez[$aa]){
												$diffH1+=1; 
											}
											$num_comparisonsH1+=1;
										}
									}		
								}
							}
						}					
					}
					# for some sites, there may be only one individual with a genotype
					if($num_comparisonsH1==0){
						for ($y = 0 ; $y <= $#H1; $y++ ) {
							if(($temp[$H1[$y]+$whotoinclude[1]-2] ne './.')&&(length($temp[$H1[$y]+$whotoinclude[1]-2]) == 3)){
								@H1_first_allelez=split('/',$temp[$H1[$y]+$whotoinclude[1]-2]);
								if($H1_first_allelez[0] ne $H1_first_allelez[1]){
									$diffH1+=1;
								}
								$num_comparisonsH1+=1;	
							}
						}	
					}	
					# now tabulate the average pairwise nucleotide diversity within H1
					if($num_comparisonsH1>0){
							# this does not depend on the position being polymorphic
								$H1_pairwise_nucleotide_diversity_per_window{$window_counter."_".$current_chromosome."_".$current_window}+=($diffH1/$num_comparisonsH1);
								$H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$window_counter."_".$current_chromosome."_".$current_window}+=1;
								# later we will standardize this by the number of sites per window
					}
					# calculate the ABBA BABBA stats, plus more
					@temp1=split('',$string);
					$x_uniq = uniq @temp1;
					if($x_uniq == 2){
						$BBAA_hash{$window_counter."_".$current_chromosome."_".$current_window}+=($H1_derived_freq*$H2_derived_freq*(1-$H3_derived_freq));
						if((((1-$H1_derived_freq)*$H2_derived_freq*$H3_derived_freq)>0) || (($H1_derived_freq*(1-$H2_derived_freq)*$H3_derived_freq)>0)){
							# this is a polymorphic position and it is an ABBA_BABA site   
							#if($current_window ==10000000 ){
							#	print $H1_derived_freq,"\t",$H2_derived_freq,"\t",$H3_derived_freq,"\n";
							#}

							$ABBA_hash{$window_counter."_".$current_chromosome."_".$current_window}+=((1-$H1_derived_freq)*$H2_derived_freq*$H3_derived_freq);
							$BABA_hash{$window_counter."_".$current_chromosome."_".$current_window}+=($H1_derived_freq*(1-$H2_derived_freq)*$H3_derived_freq);
							
							# define the doner population for the comparison between H2 and H3
							if($H2_derived_freq > $H3_derived_freq){ # H3 is the doner
								$peak_H2_H3_derived_freq = $H2_derived_freq;
							}
							else{ # H3 is the doner
								$peak_H2_H3_derived_freq = $H3_derived_freq;	
							}
							# The difference between these two will be the doner difference, which is the denominator if the site is an ABBA site
							# i.e. if $ABBA_hash{} > $BABA_hash{} 
							$ABBA_peak_hash=((1-$H1_derived_freq)*$peak_H2_H3_derived_freq*$peak_H2_H3_derived_freq);
							$BABA_peak_hash=($H1_derived_freq*(1-$peak_H2_H3_derived_freq)*$peak_H2_H3_derived_freq);
							# here we are calculating stats for f for H2 and H3.
							# we need to do this for each site because some of them can be undefined and should
							# not be included in the average
							if(($ABBA_peak_hash - $BABA_peak_hash) != 0){
								$f_H2_H3{$window_counter."_".$current_chromosome."_".$current_window} += ((((1-$H1_derived_freq)*$H2_derived_freq*$H3_derived_freq))-(($H1_derived_freq*(1-$H2_derived_freq)*$H3_derived_freq)))/ ($ABBA_peak_hash - $BABA_peak_hash);
								$f_H2_H3_counter{$window_counter."_".$current_chromosome."_".$current_window} += 1;
							}	
							# here we are calculating stats for f and the assignment of H1 and H2 needs to be switched.
							if($H1_derived_freq > $H3_derived_freq){
								$peak_H1_H3_derived_freq = $H1_derived_freq;
							}
							else{
								$peak_H1_H3_derived_freq = $H3_derived_freq;	
							}
							$BABA_peak_hashH1H3=($peak_H1_H3_derived_freq*(1-$H2_derived_freq)*$peak_H1_H3_derived_freq);
							$ABBA_peak_hashH1H3=((1-$peak_H1_H3_derived_freq)*$H2_derived_freq*$peak_H1_H3_derived_freq);
							if(($BABA_peak_hashH1H3 - $ABBA_peak_hashH1H3) != 0){
								$f_H1_H3{$window_counter."_".$current_chromosome."_".$current_window} += ((($H1_derived_freq*(1-$H2_derived_freq)*$H3_derived_freq))-(((1-$H1_derived_freq)*$H2_derived_freq*$H3_derived_freq)))/ ($BABA_peak_hashH1H3 - $ABBA_peak_hashH1H3);
								$f_H1_H3_counter{$window_counter."_".$current_chromosome."_".$current_window} += 1;
							}
							if(($H2_derived_freq >= $H1_derived_freq)&&(($ABBA_peak_hash - $BABA_peak_hash)!=0)){
								$f_dm{$window_counter."_".$current_chromosome."_".$current_window} += ((((1-$H1_derived_freq)*$H2_derived_freq*$H3_derived_freq))-(($H1_derived_freq*(1-$H2_derived_freq)*$H3_derived_freq)))/ ($ABBA_peak_hash - $BABA_peak_hash);
								$f_dm_counter{$window_counter."_".$current_chromosome."_".$current_window} += 1;
							}
							elsif(($ABBA_peak_hashH1H3 - $BABA_peak_hashH1H3)!=0){
								$f_dm{$window_counter."_".$current_chromosome."_".$current_window} += ((((1-$H1_derived_freq)*$H2_derived_freq*$H3_derived_freq))-(($H1_derived_freq*(1-$H2_derived_freq)*$H3_derived_freq)))/ -($ABBA_peak_hashH1H3 - $BABA_peak_hashH1H3);	
								$f_dm_counter{$window_counter."_".$current_chromosome."_".$current_window} += 1;
							}
	
						}
					}
				}	
			}
		}
	}
}


close DATAINPUT;

# now merge the hash keys
my @common_keys = ();

foreach (keys %ABBA_hash) {
	push(@common_keys, $_);
}

foreach (keys %BABA_hash) {
	push(@common_keys, $_) unless exists $ABBA_hash{$_};
}

my @common_keys2 = ();

foreach (keys %ABBA_hash) {
	push(@common_keys2, $_);
}

foreach (keys %BBAA_hash) {
	push(@common_keys2, $_) unless exists $ABBA_hash{$_};
}


my @out = keys %{{map {($_ => 1)} (@common_keys, @common_keys2)}};

@out = map  { $_->[0] }
             sort { $a->[1] <=> $b->[1] }
             map  { [$_, $_=~/(\d+)/] }
                 @out;



foreach (@out) {
	@temp1=split('_',$_);
	print OUTFILE $temp1[1],"\t",$temp1[2]+1,"\t",$temp1[2]+$sliding_window,"\t";
	if(defined($ABBA_hash{$_})){
		print OUTFILE $ABBA_hash{$_},"\t";
	}
	else{
		print OUTFILE "0\t";
	}
	if(defined($BABA_hash{$_})){
		print OUTFILE $BABA_hash{$_},"\t0\t0\t0\t0\n";
	}
	else{
		print OUTFILE "0\t0\t0\t0\t0\n";
	}
}
if($#out == -1){
	print OUTFILE "chr0\t1\t500000\t0\t0\t0\t0\t0\t0\n";
}

close OUTFILE;


print OUTFILE2 "chromosome\tbegin\tend\tABBA\tBABA\tBBAA\tD\tfdH2H3\tfH1H3\tf_dm\tdH2H3\tnum_sites_per_windowH2H3\tdH1H3\tnum_sites_per_windowH1H3\tH2pi\tnumsitesH2pi\tH1pi\tnumsitesH1pi\n";
foreach (@out) {
	@temp1=split('_',$_);
	print OUTFILE2 $temp1[1],"\t",$temp1[2]+1,"\t",$temp1[2]+$sliding_window,"\t";
	if(defined($ABBA_hash{$_})){
		print OUTFILE2 $ABBA_hash{$_},"\t";
	}
	else{
		print OUTFILE2 "0\t";
	}
	if(defined($BABA_hash{$_})){
		print OUTFILE2 $BABA_hash{$_},"\t";
	}
	else{
		print OUTFILE2 "0\t";
	}
	if(defined($BBAA_hash{$_})){
		print OUTFILE2 $BBAA_hash{$_},"\t";
	}
	else{
		print OUTFILE2 "0\t";
	}
	#print D for this window
	if((defined($ABBA_hash{$_}))&&(defined($BABA_hash{$_}))){
		if(($ABBA_hash{$_}+$BABA_hash{$_})>0){
			print OUTFILE2 ($ABBA_hash{$_}-$BABA_hash{$_}) / ($ABBA_hash{$_}+$BABA_hash{$_}),"\t";
		}
		else{
			print OUTFILE2 "NaN\t";
		}
	}	
	else{
		print OUTFILE2 "NaN\t";
	}
	#print fd H2H3 for this window
	if(defined($f_H2_H3_counter{$_})){
		print OUTFILE2 $f_H2_H3{$_}/$f_H2_H3_counter{$_},"\t";
	}
	else{
		print OUTFILE2 "NaN\t";
	}
	#print fd H1H3 for this window
	if(defined($f_H1_H3_counter{$_})){
		print OUTFILE2 $f_H1_H3{$_}/$f_H1_H3_counter{$_},"\t";
	}
	else{
		print OUTFILE2 "NaN\t";
	}	
	#print f_dm for this window
	if(defined($f_dm{$_})){
		print OUTFILE2 $f_dm{$_}/$f_dm_counter{$_},"\t";
	}
	else{
		print OUTFILE2 "NaN\t";
	}	
	#print average H2H3 pairwise divergence for this window and number of H2H3 sites
	if(defined($number_of_sites_per_window{$_})){
		if($number_of_sites_per_window{$_}>0){
			print OUTFILE2 ($H2_H3_pairwise_divergence_per_window{$_}/$number_of_sites_per_window{$_}),"\t",$number_of_sites_per_window{$_},"\t";
		}
		else{
			print OUTFILE2 "NaN\t",$number_of_sites_per_window{$_},"\t";
		}
	}
	else{
		print OUTFILE2 "NaN\t",$number_of_sites_per_window{$_},"\t";
	}
	#print average H1H3 pairwise divergence for this window and number of H1H3 sites
	if(defined($number_of_sites_per_windowH1H3{$_})){
		if($number_of_sites_per_windowH1H3{$_} > 0){
			print OUTFILE2 ($H1_H3_pairwise_divergence_per_window{$_}/$number_of_sites_per_windowH1H3{$_}),"\t",$number_of_sites_per_windowH1H3{$_},"\t";
		}
		else{
			print OUTFILE2 "NaN\t",$number_of_sites_per_windowH1H3{$_},"\t";
		}
	}
	else{
		print OUTFILE2 "NaN\t",$number_of_sites_per_windowH1H3{$_},"\t";
	}
	#print H2 pairwise nucleotide diversity per site for this window and number of H2 pairwise nucleotide diversity sites
	if(defined($H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_})){
		if($H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_}>0){
			print OUTFILE2 ($H2_pairwise_nucleotide_diversity_per_window{$_}/$H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_}),"\t",$H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_},"\t";
		}
		else{
			print OUTFILE2 "NaN\t",$H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_},"\t";
		}
	}
	else{
		print OUTFILE2 "NaN\t",$H2_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_},"\t";
	}
	#print H1 pairwise nucleotide diversity per site for this window and number of H1 pairwise nucleotide diversity sites
	if(defined($H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_})){
		if($H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_}>0){
			print OUTFILE2 ($H1_pairwise_nucleotide_diversity_per_window{$_}/$H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_}),"\t",$H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_},"\n";
		}
		else{
			print OUTFILE2 "NaN\t",$H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_},"\n";
		}
	}
	else{
		print OUTFILE2 "NaN\t",$H1_number_of_sites_for_pairwise_nucleotide_diversity_per_window{$_},"\n";
	}
}
if($#out == -1){
	print OUTFILE2 "chr0\t1\t500000\t0\t0\t0\t0\t0\t0\t0\n";
}

close OUTFILE2;


```

Here are some command lines:
H2 vict
perl Performs_ABBA_BABA_on_populations_diploid_outgroup.pl XLXVXGXM_merged_sorted.bam.vcf.gz.tab 11111111111111111111111111111111111111 41_4_11-12-13-14-15-16-17-18-21-22-23-24_20-25-26-27-28-29-30-31-32-33-34-35-36_1-2-3-4-5-6-7-8 O_muel_H3_allgilli_H1_Capelaev_H2_vict.jk O_muel_H3_allgilli_H1_Capelaev_H2_vict.stats

H2 not including Jonk
perl Performs_ABBA_BABA_on_populations_diploid_outgroup.pl XLXVXGXM_merged_sorted.bam.vcf.gz.tab 11111111111111111111111111111111111111 41_4_11-12-13-14-15-16-17-18-21-22-23-24_20-25-26-27-28-29-30-31-32-33-34-35-36_9-10-19 Omuel_H3_allgilli_H1_Capelaev_H2_SAlaev.jk Omuel_H3_allgilli_H1_Capelaev_H2_SAlaev.stats


H2 including Jonk
perl Performs_ABBA_BABA_on_populations_diploid_outgroup.pl XLXVXGXM_merged_sorted.bam.vcf.gz.tab 11111111111111111111111111111111111111 41_4_11-12-13-14-15-16-17-18-21-22-23-24_20-25-26-27-28-29-30-31-32-33-34-35-36_9-10-19-37 Omuel_H3_allgilli_H1_Capelaev_H2_SAlaevJonk.jk Omuel_H3_allgilli_H1_Capelaev_H2_SAlaevJonk.stats

H3 CoGHgilli, H1 CoGHlaev, H2 not including Jonk
perl Performs_ABBA_BABA_on_populations_diploid_outgroup.pl XLXVXGXM_merged_sorted.bam.vcf.gz.tab 11111111111111111111111111111111111111 41_4_11-12-13-21-22-23-24_25-26-33-34-35-36_9-10-19 Omuel_H3_CoGHgilli_H1_CoGHlaev_H2_SAlaev.jk Omuel_H3_CoGHgilli_H1_CoGHlaev_H2_SAlaev.stats

H3 Kleingilli, H1 Kleinlaev, H2 not including Jonk
perl Performs_ABBA_BABA_on_populations_diploid_outgroup.pl XLXVXGXM_merged_sorted.bam.vcf.gz.tab 11111111111111111111111111111111111111 41_4_14-15-16-17-18_20-27-28-29-30-31-32_9-10-19 Omuel_H3_Kleingilli_H1_Kleinlaev_H2_SAlaev.jk Omuel_H3_Kleingilli_H1_Kleinlaev_H2_SAlaev.stats

# Plot the results with R

Script: fdm_plot_aDNA_vs_xDNA.R

```R
# This R script will make a plot of f_dm from the Malinksy paper plotted
# against average pairwise divergence between H2 and H3 if f_dm is positive
# or between H1 and H3 if f_dm is negative. 

library (ggplot2)

pdf("O_muel_H3_allgilli_H1_Capelaev_H2_vict.stats.pdf",w=8, h=4, version="1.4", bg="transparent")
borneodata <- data.frame(matrix(NA, nrow=1, ncol=1))
switch<-0
borneodata <- borneodata[0,]
borneodata<-read.table("O_muel_H3_allgilli_H1_Capelaev_H2_vict.stats",header=TRUE)
# Make a new distance for aDNA and xDNA column that will have dH2H3 if f_dm is positive and dH1H3 if f_dm is negative
borneodata$conditional_distance <- 0
# make values of the conditional_distance column equal to dH2H3 if d_fm is positive
borneodata$conditional_distance[which(!is.na(borneodata$f_dm) & !is.na(borneodata$dH2H3) & borneodata$f_dm > 0  ) ] <- borneodata$dH2H3[which(!is.na(borneodata$f_dm) & !is.na(borneodata$dH2H3) & borneodata$f_dm > 0  )]
# make values of the conditional_distance column equal to dH1H3 if d_fm is negative
borneodata$conditional_distance[which(!is.na(borneodata$f_dm) & !is.na(borneodata$dH1H3) & borneodata$f_dm < 0)] <- borneodata$dH1H3[which(!is.na(borneodata$f_dm) & !is.na(borneodata$dH1H3) & borneodata$f_dm < 0)]
# make values of the conditional_distance column equal to the average of dH1H3 and dH2H3 if d_fm is equal to zero
borneodata$conditional_distance[which(!is.na(borneodata$f_dm) & !is.na(borneodata$dH1H3) & !is.na(borneodata$dH2H3) & borneodata$f_dm == 0)] <- (borneodata$dH1H3[which(!is.na(borneodata$f_dm) & !is.na(borneodata$dH1H3) & borneodata$f_dm == 0)]+borneodata$dH2H3[which(!is.na(borneodata$f_dm) & !is.na(borneodata$dH2H3) & borneodata$f_dm == 0)])/2
# Make a new column to distinguish aDNA and xDNA
borneodata$a_or_x <- '0'
# make values of the this column equal to xDNA for xDNA
borneodata$a_or_x[which(borneodata$chromosome == 'chr2L' & borneodata$end < 25000000 )]  <- '1'
# reorder the dataframe
borneodata<-borneodata[with(borneodata, order(a_or_x)), ]

# Plot the data
d<-ggplot(data=borneodata, aes(x=conditional_distance,y=f_dm, colour=a_or_x)) +
  theme_bw() + theme(panel.grid.minor=element_blank(),panel.grid.major=element_blank()) + 
  geom_point(size=4, alpha = 0.6) + 
  # label axis 
  labs(x=expression("Conditional distance to H3"), y=expression(paste(italic(f[DM])))) +
  # italicize the y-axis label
  #theme(axis.title.y=element_text(face="italic")) +
  # legend details
  scale_colour_manual(name="Genomic region", values = c("0"="gray", "1"="red" ),breaks=c("0", "1"),labels=c("Autosomes","Sex linked"))+
  # move the legend
  theme(legend.position = c(.8, .2)) +
  # remove boxes around legend symbols
  theme(legend.key = element_blank())+
  # draw a line on the yaxis=0
  geom_hline(yintercept=0) 
  #theme(text = element_text(size=8),axis.text.x = element_text(angle=90, vjust=1)) 
d
dev.off()
```

# Block jakknife

Script: Does_block_jackknife.pl

```perl
#!/usr/bin/env perl
use strict;
use warnings;


# This program reads in the output of the script called Performs_ABBA_BABA_on_populations.pl
# and calculates the standard error of the weighted mean value of fDM with weightings based 
# on the sum of the number of abba and baba sites in each window.

my $inputfile = $ARGV[0];

unless (open DATAINPUT, $inputfile) {
	print "Can not find the input file.\n";
	exit;
}

my @sumsites;
my $averagesites;
my @fdm_values;
my @weighted_fdm_values;
my $counter=0;
my $y;
my $x;
my @temp; 
my $weighted_fdm_values;
my $non_weighted_fdm_value;

while ( my $line = <DATAINPUT>) {
	@temp=split('\t',$line);
	if($temp[0] ne 'chromosome'){
		if((($temp[3]+$temp[4])>0)&&($temp[9] ne 'NAN')){ # only count windows where there are more than zero ABBABABA sites
			# load the number of ABBA and BABA sites for each window
			$sumsites[$counter]=$temp[3]+$temp[4];
			# add them up to eventually get the average
			$averagesites+=$temp[3]+$temp[4];
			# also load the fDM stat for each window
			$fdm_values[$counter]=$temp[9];
			# also calculate the non-weighted value
			$non_weighted_fdm_value+=$temp[9];
			# keep track of how many windows are loaded
			$counter+=1;
		}
	}	
}		

print "hi ",$#fdm_values," ",$#sumsites,"\n";

# Calculate real stat
# make $averagesites the average
$averagesites=$averagesites/($counter);

my $weighted_average=0;
# calculate the weighted average of fDM
for ($y = 0 ; $y <= $#fdm_values; $y++ ) {
	$weighted_average+=($sumsites[$y]*$fdm_values[$y]/$averagesites);
}	
print "The average number of sites per window is ",$averagesites,"\n";
print "The non-weighted average is ",sprintf("%.6f",$non_weighted_fdm_value/($counter)),"\n";
$weighted_average=$weighted_average/($counter);
print "The weighted average is ",sprintf("%.6f",$weighted_average),"\n";

# now calculate the standard error.
my @jack_sumsites;
my $jack_averagesites;
my @jack_fdm_values;
my $jack_weighted_average=0;
my @jack_weighted_fdm_values;
my @jackarray;
my $counter2=0;

for ($y = 0 ; $y < $counter; $y++ ) {
	for ($x = 0 ; $x < $counter; $x++ ) {
		# leave out one row for each jackknfe replicates
		if($y != $x){
			# load the number of ABBA and BABA sites for each window
			$jack_sumsites[$counter2]=$sumsites[$x];
			# add them up to eventually get the average
			$jack_averagesites+=$sumsites[$x];
			# also load the fDM stat for each window
			$jack_fdm_values[$counter2]=$fdm_values[$x];
			# keep track of the number of windows
			$counter2+=1;			
		}
	}
	# make the $jack_averagesites an average
	$jack_averagesites=$jack_averagesites/($counter2);
	# calculate the weighted average
	for ($x = 0 ; $x < $counter2; $x++ ) {
		$jack_weighted_average+=($jack_sumsites[$x]*$jack_fdm_values[$x]/$jack_averagesites);
	}
	push(@jackarray,($jack_weighted_average/$counter2));
	# reset variables
	$jack_weighted_average=0;
	$jack_averagesites=0;
	@jack_fdm_values=();
	$counter2=0;
}

# now calculate the variance of the jackknife replicates

# first we need the mean
my $jack_mean=0;
for ($x = 0 ; $x < $counter; $x++ ) {
	$jack_mean+=$jackarray[$x];
}
$jack_mean=$jack_mean/($counter);

my $jack_var=0;
for ($x = 0 ; $x < $counter; $x++ ) {
	$jack_var+=($jack_mean-$jackarray[$x])**2;
}

# for the sample variance, divide by (n-1)
print "jackvar ",sprintf("%.9f",$jack_var/($counter-1)),"\n";
my $sterr = sqrt($counter*($jack_var/($counter-1)));
print "The standard error of the weighted fDM is ",sprintf("%.5f",$sterr),"\n";
print "The 95\%CI of the weighted fDM is ",
sprintf("%.6f",($weighted_average-1.96*$sterr))," - ",sprintf("%.6f",($weighted_average+1.96*$sterr)),"\n";

close DATAINPUT;

```
