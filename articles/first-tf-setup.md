1. 改密碼

`passwd`

2. 在家目錄用virtualenv建立虛擬環境於tensorflow資料夾（官網教學的這個步驟有錯）

`virtualenv tensorflow`

3. 新增環境變數以使用tensorflow

```
echo export CUDA_HOME=/usr/local/cuda >> ~/.bashrc
echo export LD_LIBRARY_PATH=${CUDA_HOME}/lib64:${CUDA_HOME} >> ~/.bashrc
echo export PATH=${CUDA_HOME}/bin:${PATH} >> ~/.bashrc
```

4. 重新套用shell

`source ~/.bashrc`

5. 再使用tensorflow shell（自此就與官網相同）

`source ~/tensorflow/bin/activate`

正確的話會看到command prompt變成`(tensorflow) <user_id>@TheiaDL`

6. 安裝tensorflow於虛擬環境

```
(tensorflow)$ pip install --upgrade tensorflow-gpu  # for Python 2.7 and GPU
(tensorflow)$ pip3 install --upgrade tensorflow-gpu # for Python 3.n and GPU
```

6. 進入python

`python`

（完）