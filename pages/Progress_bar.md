---
layout: page
---

## Progress bar in R

Here is an example of creating a simple text progress bar in R to track the progress of a loop:  
<pre><code class="matlab">
# Set the maximum number of iterations
total = 10
# Create a progress bar using a base R function
pb = txtProgressBar( min = 0, max = total, style = 3 )
for (i in 1:total) {
	# Update the progress bar
	setTxtProgressBar(pb,i)
}
close(pb)
</code></pre>
The third style is nice because it also shows the percentage and indicates both the start and end of the progress bar.
