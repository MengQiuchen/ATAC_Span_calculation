# ∆ATAC Span calculation
To quantify the range of ATAC-seq signal increases caused by CRISPR/Cas9 editing

For those cell lines with extremely large accessibility changing regions (more than 100kbp), smooth.spline function in Stats R library (version 3.6.2) was used to smooth accessibility changes first. 

To prevent any potential batch effect, for each target sample bearing an editing locus, we defined samples bearing editing locus sufficiently far from (at least 1 mb) the target editing locus as control samples. 68% confidence interval of accessibility coverage was calculated within these control samples. 

For each cutting locus, the region around the cutting locus was first padded on two sides to include potential increasements and local background. The upper bound of condifence interval of its corresponding control samples was considered as background accessibility. To avoid the blurred boundary resulting from the noise of ATAC-seq, we introduced adjusted weight for each genomic locus to define the likelihood of it being influenced by CRISPR editing. 
	
We assumed that the farther away from the cutting locus, the changes of ATAC-seq signal were more likely caused by sequencing noise. A symmetrical exponential function: 𝑦=𝑒−∣∣𝑥/𝑎∣∣ was applied to address this, where, 𝑥 represents the distance to editing locus, 𝑦 represents adjusted weights, and 𝑎 is the parameter describing our confidence of treating observed changes as effective signals. The larger 𝑎 is, the adjusted weights decrease more slowly from the cutting site. The adjusted accessibility change were defined as the multiple of original change and adjusted weight at each locus. 
The loci with larger adjusted changes than threshold were defined as “significantly-increased loci” and the boundaries of significantly increased loci were defined as “accessibility-increasing regions”, whose span was called ∆ATAC Span.
	
The value of 𝑎 was selected following some rules. During the increased 1/a, if there is no noise, ∆ATAC Span increases first and becomes flat when 1/a is large enough. But due to sequencing noise, the curve is not always flat and has some impulse jumps along with the increase of 1/𝑎. Each jump means a few suspected noisy loci have been included in accessibility-increasing regions. So, 𝑎 should be selected within the first platform. And the smaller 1/𝑎 is, ∆ATAC Span will be more resistant to noise. Otherwise, it will get more complete increased signal regions. 
