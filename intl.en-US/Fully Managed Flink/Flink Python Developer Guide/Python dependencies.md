# Python dependencies

You can use custom Python virtual environments, third-party Python packages, JAR packages, and data files in Flink Python jobs. This topic describes how to use these dependencies in Python jobs.

This topic describes how to use the following Python dependencies:

-   [Use a custom Python virtual environment](#section_mug_8wi_mom)
-   [Use a third-party Python package](#section_bed_4cc_lj2)
-   [Use a JAR package](#section_ujt_wpb_h1k)
-   [Use data files](#section_hxe_2xw_amg)

## Use a custom Python virtual environment

Only virtual environments of Python 3.7 are supported. This section describes how to prepare a virtual environment of Python 3.7.

1.  Prepare a virtual environment of Python 3.7.

    1.  Prepare the bulid.sh script on your on-premises machine. The following code shows the content of the script.

        ```
        #!/bin/bash
        set -e -x
        yum install -y zip wget
        
        cd /root/
        bash /build/setup-pyflink-virtual-env.sh
        mv venv.zip /build/
        ```

    2.  Prepare the setup-pyflink-virtual-env.sh script on your on-premises machine. The following code shows the content of the script.

        ```
        set -e
        # Download the miniconda.sh script of Python 3.7. 
        wget "https://repo.continuum.io/miniconda/Miniconda3-py37_4.9.2-Linux-x86_64.sh" -O "miniconda.sh"
        
        # Add the execution permissions to the miniconda.sh script of Python 3.7. 
        chmod +x miniconda.sh
        
        # Create a Python virtual environment. 
        ./miniconda.sh -b -p venv
        
        # Activate the Conda Python virtual environment. 
        source venv/bin/activate ""
        
        # Install the PyFlink dependency. 
        # update the PyFlink version if needed
        pip install "apache-flink==1.12.1"
        
        # Deactivate the Conda Python virtual environment. 
        conda deactivate
        
        # Delete the cached packages. 
        rm -rf venv/pkgs
        
        # Package the prepared Conda Python virtual environment. 
        zip -r venv.zip venv
        ```

    3.  In the Command Prompt, run the following command:

        ```
        docker run -it --rm -v $PWD:/build  -w /build quay.io/pypa/manylinux2014_x86_64 ./build.sh
        ```

        After the command is run, a file named venv.zip is generated. This file indicates the virtual environment of Python 3.7.

        You can also modify the preceding script to install the required third-party Python package in the virtual environment.

2.  Use the virtual environment of Python 3.7 in Python jobs.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Artifacts**. In the upper-right corner of the page, click Upload Artifacts. In the dialog box that appears, select the venv.zip package.

        **Note:** The maximum size of files that can be uploaded in the console of fully managed Flink is 200 MB. However, the size of the file that includes the Python virtual environment exceeds this limit in most cases. In this case, you must upload the file in the [OSS console](https://oss.console.aliyun.com/).

    4.  On the Drafts page of the required Python job, select the venv.zip file in the **Python Archives** section.

    5.  On the right side of the page, click the **Advanced** tab. In the **Additional Configuration** section, add the following configuration information:

        ```
        python.executable: venv.zip/venv/bin/python
        ```

    6.  Click **Save**.


## Use a third-party Python package

The following two scenarios show you how to use a third-party Python package:

-   Use a third-party Python package that can be directly imported

    If your third-party Python package is a [Zip Safe](https://setuptools.readthedocs.io/en/latest/userguide/miscellaneous.html?highlight=zip_safe#setting-the-zip-safe-flag) package, it can be directly used in Python jobs without installation. To use such a package, perform the following steps:

    1.  Download a third-party Python package that can be directly imported.
        1.  Visit [PyPI](https://pypi.org/) on your web browser.
        2.  Enter the name of a third-party Python package, such as apache-flink1.12.2, in the search box.
        3.  In the search results, click the name of the required package.
        4.  In the left-side navigation pane of the page that appears, click Download files.
        5.  Click the name of the package whose name contains cp37-cp37m-manylinux1 to download the package.
    2.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).
    3.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.
    4.  In the left-side navigation pane, click **Artifacts**. In the upper-right corner of the page, click Upload Artifacts. In the dialog box that appears, select a third-party Python package.
    5.  On the Drafts page of the required Python job, select the uploaded third-party Python package in the Python Libraries section.
    6.  Click **Save**.
-   Use a third-party Python package that needs to be compiled

    If a third-party Python package meets the following conditions, it needs to be compiled before it can be used: The third-party Python package is a compressed package in the tar.gz format or a source package that you downloaded from another location, and the setup.py file exists under the root directory of the compressed package. You must compile a third-party Python package in an environment that is compatible with fully managed Flink before you can call the third-party Python package in a Python job.

    We recommend that you use Python 3.7 in the quay.io/pypa/manylinux2014\_x86\_64 image to compile third-party Python packages. The packages generated by the image are compatible with most Linux operating systems. For more information about the image, see [manylinux](https://github.com/pypa/manylinux).

    **Note:** Python 3.7 is installed in the /opt/python/cp37-cp37m/bin/python3 directory.

    The following example demonstrates how to compile and use the third-party Python package opencv-python-headless.

    1.  Compile a third-party Python package.
        1.  Prepare the bulid.sh script on your on-premises machine. The following code shows the content of the script.

            ```
            #!/bin/bash
            set -e -x
            yum install -y zip
            
            PYBIN=/opt/python/cp37-cp37m/bin
            
            "${PYBIN}/pip" install --target __pypackages__ -r requirements.txt --no-deps
            cd __pypackages__ && zip -r deps.zip . && mv deps.zip ../ && cd ..
            rm -rf __pypackages__
            ```

        2.  Prepare the requirements.txt file on your on-premises machine. The following code shows the content of the file.

            ```
            opencv-python-headless
            ```

        3.  In the Command Prompt, run the following command:

            ```
            docker run -it --rm -v $PWD:/build  -w /build quay.io/pypa/manylinux2014_x86_64 /bin/bash build.sh
            ```

            After the command is run, a file named deps.zip is generated. This file is the compiled third-party Python package.

            You can also modify the content of the requirements.txt file to install other required third-party Python packages. In addition, multiple Python dependencies can be specified in the requirements.txt file.

    2.  Use the third-party Python package deps.zip in Python jobs.
        1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).
        2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.
        3.  In the left-side navigation pane, click **Artifacts**. In the upper-right corner of the page, click Upload Artifacts. In the dialog box that appears, select the deps.zip package.

            **Note:** The maximum size of files that can be uploaded in the console of fully managed Flink is 200 MB. However, the size of the file that includes the Python virtual environment exceeds this limit in most cases. In this case, you must upload the file in the Object Storage Service \(OSS\) console.

        4.  On the Drafts page of the required Python job, select the deps.zip package in the **Python Libraries** section.
        5.  Click **Save**.

## Use a JAR package

If you use Java classes, such as a connector or a Java user-defined function, in your Flink Python job, you can perform the following operations to specify the JAR package of the connector or Java user-defined function.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Artifacts**. In the upper-right corner of the page, click Upload Artifacts. In the dialog box that appears, select the JAR package that you want to use.

4.  On the Drafts page of the required Python job, select the uploaded JAR package in the Additional Dependencies section.

5.  On the right side of the page, click the **Advanced** tab. In the **Additional Configuration** section, add the following configuration information:

    For example, if the job depends on the two JAR packages that are named jar1.jar and jar2.jar, add the following configuration information:

    ```
    pipeline.classpaths: 'file:///flink/usrlib/jar1.jar;file:///flink/usrlib/jar2.jar'
    ```

6.  Click **Save**.


## Use data files

The following scenarios show you how to use data files:

-   Select a package in the Python Archives section

    If you have a large number of data files, you can package the data files into a ZIP file and perform the following operations to use them in Python jobs: To use such a package, perform the following steps:

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).
    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.
    3.  In the left-side navigation pane, click **Artifacts**. In the upper-right corner of the page, click Upload Artifacts. In the dialog box that appears, select the ZIP package that you want to upload.
    4.  On the Drafts page of the required Python job, select the uploaded ZIP package in the Python Archives section.
    5.  In Python user-defined functions, you can use the following method to access data files. In the following example, the name of the package that contains the data files is mydata.zip.

        ```
        def map():
            with open("mydata.zip/mydata/data.txt") as f:
            ...
        ```

-   Select a data file in the Additional Dependencies section

    If you have a small number of data files, you can perform the following operations to access these files in Python jobs: To use such a package, perform the following steps:

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).
    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.
    3.  In the left-side navigation pane, click **Artifacts**. In the upper-right corner of the page, click Upload Artifacts. In the dialog box that appears, select the ZIP package that you want to upload.
    4.  On the Drafts page of the required Python job, select the data file that you want to use in the Additional Dependencies section.
    5.  In Python user-defined functions, you can use the following method to access data files. In this example, the name of the data file is data.txt.

        ```
        def map():
            with open("/flink/usrlib/data.txt") as f:
            ...
        ```


