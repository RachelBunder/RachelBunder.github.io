---
layout: single
author_profile: true
published: true
date: 2020-02-07 12:00 f+1000
classes: wide
---

Lambda is AWS's serverless computing platform: you write some code, set up triggers for when it executes and AWS takes care of the resourcing. Python is one of it supported languages, but by default it doesn't include much beyond the standard lib.


![AWS Lambda does not have the request layer as default](/assets/posts/20200208_aws_layers/aws_lambda_requests_fail.png)


To can include other packages by using an AWS lambda layer. A lambda layer is a way that you can share code between different lambda functions, perfect for pacakges.

First, as we are installing new packages, you will want to create an virtual environment, so to not effect the rest of your python environment.


```sh
virtualenv -p python3 requests_lambda_layer
. bom_lambda_layer/bin/activate
```

To include packages in a layer, you need to imitate the python folder structure, install your packages in that location, zip and then upload as a layer. You can have up to 5 layers for a lambda function and in total the unzipped size must be less than 250MB.


```sh
mkdir -p requests_lambda_layer/python/lib/python3.8/site-packages
pip install requests -t requests_lambda_layer/python/lib/python3.8/site-packages/.
cd requests_lambda_layer
zip -r ../requests_lambda_layer.zip *
```

You can put multiple package in each layer, but you may want to split them if you are using the same layer for multiple functions.


Now that you have a zip file, you want to make a Lambda Layer. In the AWS console go to Lambda and then to Layers and Create Layer and fill out the details:

![Creating the AWS lambda layer](/assets/posts/20200208_aws_layers/aws_create_layer.png)


Now that you have the layer, we need to add it to the lambda function. Select Layers under the lambda function and then "Add a layer"

![Success after adding an AWS layer with the requests package](/assets/posts/20200208_aws_layers/aws_lambda_requests_success.png)

## For pandas, numpy and matplotlib
This generally doesn't work for pandas or numpy as they come with compiled code that is OS specific. Unless you are creating your layers in the same systems (or in a docker container that mimics the lambda environment), it won't work when you upload it as a layer. If you try, you will get an error message like this:

```"errorMessage": "Unable to import module 'lambda_function': Unable to import required dependencies```

![Second, but different fail after ](/assets/posts/20200208_aws_layers/aws_lambda_pandas_fail2.png)


To get around this, you can install the specific linux version of numpy and pandas. PyPi has wheel files of most libaries, including __[pandas](https://pypi.org/project/pandas/#files)__ and __[numpy](https://pypi.org/project/numpy/#files)__. A wheel file is essentiall a .zip that contains python packages with their native dependeicies. For AWS lambda you will want the versions ending with `manylinux1_x86_64.whl` for whatever version of python you are using.

Even though we are using the panda wheel for manylinux, the numpy wheel also needs to be installed separately using the `--upgrade` parameter
```sh
# Setting up a venv
virtualenv -p python3 pandas_example
. pandas_matplotlib_example/bin/activate


mkdir -p pandas_lambda_layer/lib/python3.8/site-packages

# Installing all the C dependent packages. Make sure htey are all manylinux and x86
pip install pandas-1.0.5-cp38-cp38-manylinux1_x86_64.whl -t pandas_lambda_layer/python/lib/python3.8/site-packages/.
pip install numpy-1.19.1-cp38-cp38-manylinux1_x86_64.whl -t pandas_lambda_layer/python/lib/python3.8/site-packages/. --upgrade

# zip all files
cd pandas_lambda_layer
zip -r ../pandas_lambda_layer.zip * --quiet
cd ..

# Deactivate and remove venv
deactivate
# Remove  virtual env, and all non-zipped files
rm -r pandas_example
rm -r panda_lambda_layer
```

To get matplotlib, we need to install  __[pillow](https://pypi.org/project/pillow/#files)__ and __[kiwisolver](https://pypi.org/project/kiwisolver/#files)__ and kiwisolver from the wheel files as well:


```sh
# Setting up a venv
virtualenv -p python3 panda_matplotlib_example
. panda_matplotlib_example/bin/activate


mkdir -p panda_matplotlib_lambda_layer/lib/python3.8/site-packages


# Installing all the C dependent packages. Make sure htey are all manylinux and x86
pip install pandas-1.0.5-cp38-cp38-manylinux1_x86_64.whl -t panda_matplotlib_lambda_layer/python/lib/python3.8/site-packages/.
pip install matplotlib-3.3.0-cp38-cp38-manylinux1_x86_64.whl -t panda_matplotlib_lambda_layer/python/lib/python3.8/site-packages/. --upgrade
pip install kiwisolver-1.2.0-cp38-cp38-manylinux1_x86_64.whl  -t panda_matplotlib_lambda_layer/python/lib/python3.8/site-packages/. --upgrade
pip install Pillow-7.2.0-cp38-cp38-manylinux1_x86_64.whl -t panda_matplotlib_lambda_layer/python/lib/python3.8/site-packages/. --upgrade
pip install numpy-1.19.1-cp38-cp38-manylinux1_x86_64.whl -t panda_matplotlib_lambda_layer/python/lib/python3.8/site-packages/. --upgrade

# zip all files
cd panda_matplotlib_lambda_layer
zip -r ../panda_matplotlib_lambda_layer.zip * --quiet
cd ..

# Deactivate and remove venv
deactivate
# Remove  virtual env, and all non-zipped files
rm -r panda_matplotlib_example
rm -r panda_matplotlib_lambda_layer
```

As these packages all depend on each other I find it much easier to install them in the same lambda layer.

I did try to get seaborn set up, but seaborn requires SciPy and that took the unzipped code over the 250MB limit. I also tried to get plotly working, but struggled with finding the appropriate wheel files for psutil.


