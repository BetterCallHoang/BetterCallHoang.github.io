---
layout: post
title: "A way to improve the start-up time for Matlab"
date: 2025-03-25
---



<script type="text/javascript">
  window.MathJax = {
    tex: {
      inlineMath: [['$', '$'], ['\\(', '\\)']],  // Enable $...$ and \( ... \)
      displayMath: [['$$', '$$'], ['\\[', '\\]']] // Enable $$...$$ and \[ ... \]
    },
    svg: {
      fontCache: 'global'
    }
  };
</script>
<script type="text/javascript" async
  src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.js"></script>





I recently found a good way to decrease the start-up time running of Matlab.
Here is [the original post](https://www.mathworks.com/matlabcentral/answers/717043-how-can-i-speed-up-my-matlab-startup-time).

In the most cases, the most consuming run time for start-up time of Matlab is "InitSunVM" task. We can determine which the tasks included in start-up time by go to the [Installed Matlab folder Path]/bin and open the Terminal, then type `matlab -timming` to see the log of start-up tasks's time.  
![alt text](/assets/Matlab-performance-improve/image.png)

If the most time-consuming task is the "InitSunVM", let's follow these below steps to fix, otherwise go to the above post for find other reasons.

The reason that make "InitSunVM" take so long to complete is caused by the Anti Virus Applications, in my case is Windows Security (I'am using Windows 11). Our method to fix it is explicits the installed Matlab folder in the Virus Scan Protection, which make our start-up process become so long.

Here is the steps:
1. Go to Setting>Privacy & Security>Windows Security>Virus & threat protection
![alt text](/assets/Matlab-performance-improve/image-1.png)
2. In Virus & threat protection settings, choose "Manage Setting"
![alt text](/assets/Matlab-performance-improve/image-2.png)
3. Scroll down to the Exclusions section, then choose "Add or remove exclusions", then select the installed Matlab folder.
![alt text](/assets/Matlab-performance-improve/image-3.png)
And that's it, now the virus scan feature of Windows Security will ignore the Maltab when Matlab start. So the performance of start-up time is incresed, from nearly 30s to 6s in my case:
![alt text](/assets/Matlab-performance-improve/image-4.png)

Thank for reading!. If you have any methods to improve the performance, just comment to the comment section!. 

