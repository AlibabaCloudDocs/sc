# 使用Python依赖

您可以在Flink Python作业中使用自定义的Python虚拟环境、第三方Python包、JAR包和数据文件等，本文为您介绍如何在Python作业中使用这些依赖。

本文通过以下场景为您介绍如何使用Python依赖：

-   [使用自定义的Python虚拟环境](#section_mug_8wi_mom)
-   [使用第三方Python包](#section_bed_4cc_lj2)
-   [使用JAR包](#section_ujt_wpb_h1k)
-   [使用数据文件](#section_hxe_2xw_amg)

## 使用自定义的Python虚拟环境

当前仅支持Python 3.7的Python虚拟环境，下文为您介绍如何准备Python 3.7的虚拟环境。

1.  准备Python 3.7的虚拟环境。

    1.  在本地准备setup-pyflink-virtual-env.sh脚本，其内容如下。

        ```
        set -e
        # 下载Python 3.7 miniconda.sh脚本。
        wget "https://repo.continuum.io/miniconda/Miniconda3-py37_4.9.2-Linux-x86_64.sh" -O "miniconda.sh"
        
        # 为Python 3.7 miniconda.sh脚本添加执行权限。
        chmod +x miniconda.sh
        
        # 创建Python的虚拟环境。
        ./miniconda.sh -b -p venv
        
        # 激活Conda Python虚拟环境。
        source venv/bin/activate ""
        
        # 安装PyFlink依赖。
        # update the PyFlink version if needed
        pip install "apache-flink==1.12.1"
        
        # 关闭Conda Python虚拟环境。
        conda deactivate
        
        # 删除缓存的包。
        rm -rf venv/pkgs
        
        # 将准备好的Conda Python虚拟环境打包。
        zip -r venv.zip venv
        ```

    2.  在本地准备bulid.sh脚本，其内容如下。

        ```
        #!/bin/bash
        set -e -x
        yum install -y zip wget
        
        cd /root/
        bash /build/setup-pyflink-virtual-env.sh
        mv venv.zip /build/
        ```

    3.  在CMD命令行，执行如下命令。

        ```
        docker run -it --rm -v $PWD:/build  -w /build quay.io/pypa/manylinux2014_x86_64 ./build.sh
        ```

        执行完该命令后，会生成一个名字为venv.zip的文件，即为Python 3.7的虚拟环境。

        您也可以修改上述脚本，在虚拟环境中安装所需的第三方Python包。

2.  在Python作业中使用Python 3.7虚拟环境。

    1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。

    2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

    3.  在左侧导航栏，单击**资源上传**，上传venv.zip文件。

        **说明：** Flink全托管开发控制台上可以上传文件大小的限制为200 MB，而Python虚拟环境的大小通常会超过该限制，因此，您需要通过[OSS管理控制台](https://oss.console.aliyun.com/)上传文件。

    4.  在目标作业开发页面的**Python Archives**项，选择venv.zip文件。

    5.  单击右侧的**高级配置**，在**更多Flink配置**项，添加配置信息。

        ```
        python.executable: venv.zip/venv/bin/python
        ```

    6.  单击**保存**。


## 使用第三方Python包

下面将从以下两个场景为您介绍如何使用第三方Python包：

-   使用可直接Import的第三方Python包

    如果您的第三方Python包是[Zip Safe](https://setuptools.readthedocs.io/en/latest/userguide/miscellaneous.html?highlight=zip_safe#setting-the-zip-safe-flag)的，即不需要安装即可直接在Python作业中使用。操作步骤如下：

    1.  下载可直接Import的第三方Python包。
        1.  在浏览器上打开[PyPI](https://pypi.org/)页面。
        2.  在搜索框中输入目标第三方Python包名称，例如apache-flink 1.12.2。
        3.  在搜索结果中，单击目标结果名称。
        4.  在左侧导航栏，单击Download files。
        5.  单击文件名中包含cp37-cp37m-manylinux1的包名称进行下载。
    2.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。
    3.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。
    4.  在左侧导航栏，单击**资源上传**，上传第三方Python包。
    5.  在目标作业开发页面的Python Libraries项，选择所上传的第三方Python包。
    6.  单击**保存**。
-   使用需要编译的第三方Python包

    如果您的第三方Python包是tar.gz格式的压缩包，或从其他地方下载的源码包，且压缩包的根目录下存在setup.py文件，则这种类型的第三方Python包通常需要先编译才能被使用。您需要先在与Flink全托管兼容的环境下编译第三方Python包，然后才可在Python作业中调用第三方Python包。

    推荐使用quay.io/pypa/manylinux2014\_x86\_64镜像容器中的Python 3.7来编译第三方Python包，使用该容器编译生成的包兼容绝大多数Linux环境，关于该镜像容器的更多信息请参见[manylinux](https://github.com/pypa/manylinux)。

    **说明：** Python 3.7的安装路径为/opt/python/cp37-cp37m/bin/python3。

    下面以opencv-python-headless第三方Python包为例，介绍一下如何编译和使用该第三方Python包。

    1.  编译第三方Python包。
        1.  在本地准备requirements.txt文件，其内容如下。

            ```
            opencv-python-headless
            ```

        2.  在本地准备build.sh脚本，其内容如下。

            ```
            #!/bin/bash
            set -e -x
            yum install -y zip
            
            PYBIN=/opt/python/cp37-cp37m/bin
            
            "${PYBIN}/pip" install --target __pypackages__ -r requirements.txt --no-deps
            cd __pypackages__ && zip -r deps.zip . && mv deps.zip ../ && cd ..
            rm -rf __pypackages__
            ```

        3.  在CMD命令行，执行如下命令。

            ```
            docker run -it --rm -v $PWD:/build  -w /build quay.io/pypa/manylinux2014_x86_64 /bin/bash build.sh
            ```

            该命令执行完后，会生成一个名字为deps.zip的文件，该文件为编译之后的第三方Python包。

            您也可以修改requirements.txt，安装其他所需的第三方Python包。此外，requirements.txt文件中可以指定多个Python依赖。

    2.  在Python作业中使用第三方Python包deps.zip。
        1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。
        2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。
        3.  在左侧导航栏，单击**资源上传**，上传deps.zip。

            **说明：** Flink全托管产品限制可上传的最大文件大小为200 MB，而Python虚拟环境的大小通常会超过该限制，需要通过OSS控制台进行文件上传。

        4.  在目标作业开发页面的**Python Libraries**项，选择deps.zip。
        5.  单击**保存**。

## 使用JAR包

如果您的Flink Python作业中使用了Java类，例如作业中使用了Connector或者Java自定义函数时，可以通过如下方式来指定Connector或者Java自定义函数的JAR包。

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**资源上传**，上传需要使用的JAR包。

4.  在目标作业开发页面的附加依赖文件项，选择需要使用的JAR包。

5.  单击右侧的**高级配置**，在**更多Flink配置**项，添加配置信息。

    假如需要依赖多个JAR包，且名字分别为jar1.jar和jar2.jar，配置内容如下。

    ```
    pipeline.classpaths: 'file:///flink/usrlib/jar1.jar;file:///flink/usrlib/jar2.jar'
    ```

6.  单击**保存**。


## 使用数据文件

下面将从两个场景为您介绍如何使用数据文件：

-   通过Python Archives选项方式

    如果您的数据文件的数量比较多时，您可以将数据文件打包成一个ZIP包，然后通过如下方式在Python作业中使用。操作步骤如下：

    1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。
    2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。
    3.  在左侧导航栏，单击**资源上传**，上传需要数据文件ZIP包。
    4.  在目标作业开发页面的Python Archives项，选择需要使用的数据文件ZIP包。
    5.  在Python自定义函数中，可以通过如下方式访问数据文件。假如数据文件所在压缩包名称为mydata.zip。

        ```
        def map():
            with open("mydata.zip/mydata/data.txt") as f:
            ...
        ```

-   通过附加依赖文件选项

    如果您的数据文件数量比较少时，可以通过如下方式在Python作业中使用。操作步骤如下：

    1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。
    2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。
    3.  在左侧导航栏，单击**资源上传**，上传需要的数据文件ZIP包。
    4.  在目标作业开发页面的附加依赖文件项，选择需要的数据文件。
    5.  在Python自定义函数中，可以通过如下方式访问数据文件。假如数据文件名称为data.txt。

        ```
        def map():
            with open("/flink/usrlib/data.txt") as f:
            ...
        ```


