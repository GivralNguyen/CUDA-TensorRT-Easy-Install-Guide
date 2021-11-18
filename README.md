---


---

<h1 id="cuda-10.2-tensorrt-8.0-easy-installation-guide">CUDA 10.2 TENSORRT 8.0 EASY INSTALLATION GUIDE</h1>
<p>If you ever find installing CUDA, CUDNN and TensorRT to be a pain in the @SS, here is the guide for you. Dummy-proof method to install TensorRT and CUDA/CUDNN. I’m going to assume that you have already installed nvidia drivers ( since it’s the easy part).</p>
<ul>
<li><a href="#Install-CUDA-Toolkit">CUDA Toolkit</a></li>
<li><a href="#Install-cuDNN">cuDNN</a></li>
<li><a href="#Install-TensorRT">TensorRT</a></li>
</ul>
<h2 id="recommendation">Recommendation</h2>
<blockquote>
<p>Debian installation method is recommended for all CUDA toolkit, cuDNN and TensorRT installation.</p>
</blockquote>
<blockquote>
<p>CUDA 10.2 is recommended</p>
</blockquote>
<blockquote>
<p>Ubuntu 18.04 is recommended</p>
</blockquote>
<h2 id="install-cuda-toolkit">Install CUDA Toolkit</h2>
<h3 id="clean-up">1. Clean up</h3>
<p><strong>a.</strong> Open a terminal window and type the following three commands to get rid of any NVIDIA/CUDA packages you may already have installed:</p>
<pre><code>sudo rm /etc/apt/sources.list.d/cuda*  
sudo apt remove --autoremove nvidia-cuda-toolkit  
sudo apt remove --autoremove nvidia-*
</code></pre>
<p><strong>b.</strong> Purge any  remaining NVIDIA configuration files and the associated dependencies that they may have been installed with.</p>
<pre><code>sudo apt-get purge nvidia*  
sudo apt-get autoremove  
sudo apt-get autoclean
</code></pre>
<p><strong>c.</strong> Remove any existing CUDA folders you may have in <strong>/usr/local/</strong></p>
<blockquote>
<p>This threw me for a loop the first several times I tried installing CUDA. &gt;There shouldn’t be any folders with the name “<strong>cuda</strong>” or “<strong>cuda-anything</strong>” in <strong>usr/local/</strong> at this point!</p>
</blockquote>
<pre><code>sudo rm -rf /usr/local/cuda*
</code></pre>
<h3 id="install">2. Install</h3>
<p><strong>(a)</strong> Setup your CUDA PPA. (Read <a href="https://itsfoss.com/ppa-guide/">here</a> if you’re curious about what a PPA is.)</p>
<blockquote>
<p>Essentially, we’re adding CUDA to our <strong>sources.list</strong>, which is the file that’s referenced any time we use the <strong>apt</strong> package manager to download stuff in the terminal with a command like “sudo apt update”</p>
</blockquote>
<pre><code>sudo apt update  
sudo add-apt-repository ppa:graphics-drivers
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo bash -c 'echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" &gt; /etc/apt/sources.list.d/cuda.list'
sudo bash -c 'echo "deb http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64 /" &gt; /etc/apt/sources.list.d/cuda_learn.list'
</code></pre>
<p><strong>(b)</strong> Install CUDA 10.1 packages, including the CuDNN library</p>
<pre><code>sudo apt update  
sudo apt install cuda-10-2  
sudo apt install libcudnn7
</code></pre>
<h3 id="add-cuda-to-path">3. Add CUDA to PATH</h3>
<p><strong>(a)</strong> After installing, we need to add CUDA to our PATH, so that the shell knows where to find CUDA. To edit our path, open up the ‘<strong>.profile</strong>’ file using nano.</p>
<pre><code>sudo nano ~/.profile
</code></pre>
<p><strong>(b)</strong> Finally, add these lines to the end of the file.</p>
<pre><code># set PATH for cuda 10.2 installation  
if [ -d "/usr/local/cuda-10.2/bin/" ]; then  
    export PATH=/usr/local/cuda-10.2/bin${PATH:+:${PATH}}  
    export LD_LIBRARY_PATH=/usr/local/cuda-10.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}  
fi
</code></pre>
<h3 id="reboot">4. Reboot</h3>
<p><strong>(a)</strong> Reboot your computer (as you should after any driver install)</p>
<h3 id="final-check">5. Final Check</h3>
<p>Now, let’s check to make sure everything’s set up!</p>
<p><strong>(a)</strong> Check NVIDIA Drivers:</p>
<pre><code>nvidia-smi
</code></pre>
<p><strong>(b)</strong> Check CUDA:</p>
<pre><code>nvcc --version
</code></pre>
<p><strong>©</strong> Check CuDNN</p>
<pre><code>/sbin/ldconfig -N -v $(sed ‘s/:/ /’ &lt;&lt;&lt; $LD_LIBRARY_PATH) 2&gt;/dev/null | grep libcudnn
</code></pre>
<p>As you long as these three checks didn’t throw you any nasty error messages, you’re all set!</p>
<h2 id="install-tensorrt">Install TensorRT</h2>
<ul>
<li><a href="https://docs.nvidia.com/deeplearning/sdk/tensorrt-install-guide/index.html#downloading">Download</a> the TensorRT local repo file that matches the Ubuntu version you are using.</li>
<li>Install TensorRT from the Debian local repo package.<br>
<strong>Note:</strong> Before issuing the following commands, you’ll need to replace <code>ubuntu1x04</code>, <code>cudax.x</code>, <code>trt4.x.x.x</code> and <code>yyyymmdd</code> with your specific OS version, CUDA version, TensorRT version and package date (according to the TensorRT deb file you download before)</li>
</ul>
<pre><code>os="ubuntuxx04"
tag="cudax.x-trt8.x.x.x-yyyymmdd"
sudo dpkg -i nv-tensorrt-repo-${os}-${tag}_1-1_amd64.deb
sudo apt-key add /var/nv-tensorrt-repo-${os}-${tag}/7fa2af80.pub

sudo apt-get update
sudo apt-get install tensorrt
</code></pre>
<ul>
<li>You might encounter error like this:</li>
</ul>
<pre><code> tensorrt : Depends: libnvinfer8 (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvinfer-plugin8 (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvparsers8 (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvonnxparsers8 (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvinfer-bin (= 8.0.3-1+cuda11.3) but it is not going to be installed
            Depends: libnvinfer-dev (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvinfer-plugin-dev (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvparsers-dev (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvonnxparsers-dev (= 8.0.3-1+cuda11.3) but 8.2.0-1+cuda11.4 is to be installed
            Depends: libnvinfer-samples (= 8.0.3-1+cuda11.3) but it is not going to be installed
            Depends: libnvinfer-doc (= 8.0.3-1+cuda11.3) but it is not going to be installed
</code></pre>
<ul>
<li>The reason for this error is as Linus Torvald once said: <a href="https://www.youtube.com/watch?v=iYWzMvlj2RQ">Nvidia, Fuck You</a>. To fix this , you have to specify the correct verison of TensorRT. Tou can go to /var/ to check the deb file. In my case it is  tensorrt_8.0.3.4-1+cuda10.2_amd64.deb</li>
</ul>
<pre><code>cd /var/nv-tensorrt-repo-ubuntu1804-cuda10.2-trt8.0.3.4-ga-20210831
sudo dpkg -i *
sudo apt-get install tensorrt=8.0.3.4-1+cuda10.2

</code></pre>
<ul>
<li>Verify the Installation</li>
</ul>
<pre><code>dpkg -l | grep TensorRT
</code></pre>
<ul>
<li>You should see something similar to the following:</li>
</ul>
<pre><code>ii  graphsurgeon-tf                                             8.0.3-1+cuda10.2                                amd64        GraphSurgeon for TensorRT package
ii  libnvinfer-bin                                              8.0.3-1+cuda10.2                                amd64        TensorRT binaries
ii  libnvinfer-dev                                              8.0.3-1+cuda10.2                                amd64        TensorRT development libraries and headers
ii  libnvinfer-doc                                              8.0.3-1+cuda10.2                                all          TensorRT documentation
ii  libnvinfer-plugin-dev                                       8.0.3-1+cuda10.2                                amd64        TensorRT plugin libraries
ii  libnvinfer-plugin8                                          8.0.3-1+cuda10.2                                amd64        TensorRT plugin libraries
ii  libnvinfer-samples                                          8.0.3-1+cuda10.2                                all          TensorRT samples
ii  libnvinfer8                                                 8.0.3-1+cuda10.2                                amd64        TensorRT runtime libraries
ii  libnvonnxparsers-dev                                        8.0.3-1+cuda10.2                                amd64        TensorRT ONNX libraries
ii  libnvonnxparsers8                                           8.0.3-1+cuda10.2                                amd64        TensorRT ONNX libraries
ii  libnvparsers-dev                                            8.0.3-1+cuda10.2                                amd64        TensorRT parsers libraries
ii  libnvparsers8                                               8.0.3-1+cuda10.2                                amd64        TensorRT parsers libraries
ii  onnx-graphsurgeon                                           8.0.3-1+cuda10.2                                amd64        ONNX GraphSurgeon for TensorRT package
ii  python3-libnvinfer                                          8.0.3-1+cuda10.2                                amd64        Python 3 bindings for TensorRT
ii  python3-libnvinfer-dev                                      8.0.3-1+cuda10.2                                amd64        Python 3 development package for TensorRT
ii  tensorrt                                                    8.0.3.4-1+cuda10.2                              amd64        Meta package of TensorRT
ii  uff-converter-tf                                            8.0.3-1+cuda10.2                                amd64        UFF converter for TensorRT package
</code></pre>
<p>There you go ! You did it. Hope this guide save you from the misery that NVIDIA has blessed upon me.<br>
One final word:</p>
<p><img src="https://i1.wp.com/itsfoss.com/wp-content/uploads/2012/09/Linus-Torvalds-Fuck-You-Nvidia.jpg?resize=600%2C339&amp;ssl=1" alt="FUCK YOU, NVIDIA "></p>

