# Tutorial

* Last updated: For release [pLink v3.1.4-preview](https://github.com/NorthblueM/pLink3-preview/releases/tag/v3.1.4)

---

[TOC]

---

## Linux version

* The pLink3 Linux version is run via command line using a `.plink` parameter file.


### Usage

1. **Create a `.plink` parameter file**

* The `.plink` file is a plain text file.
* You can first generate the `.plink` parameter file automatically via the GUI of the Windows version.
* Main parameters to modify:

  * `result_output_path`: path for result output, eg. `/home/test_data`
  * `spec_path1`, `spec_path2`, ... : paths to the `RAW` files

    * **Note**: file suffixes needs to be changed from `.raw` or `.d` to `_XXX.pf2`:
      * **Orbitrap** analyzer: `_HCDFT.pf2`
      * **Astral** analyzer: `_HCDAST.pf2`
      * **TimsTOF** instrument: `_CIDTOF.pf2`
  * `spec_num`: total number of `RAW` files

    * For example:
        ```ini
        spec_type = pf
        spec_num = 3
        spec_path1 = /home/dataset/xl/E1_HCDFT.pf2
        spec_path2 = /home/dataset/xl/E2_HCDFT.pf2
        spec_path3 = /home/dataset/xl/E3_HCDFT.pf2
        ```
  * `db_name`: the database name configured in `db.ini`, usually the FASTA filename (without the `.fasta` extension).
  

* **Add database `.fasta` entries**

  * Edit the `db.ini` file, which is located in the `bin` folder of the pLink software directory.
    * Open it with a text editor.

  * Following the format of `db1`, `path1`, add entries like `db2`, `path2`, etc. Don't forget to update the `total` number at the top.
    ```ini
    [db]
    total=1

    db1=example_db
    path1=/home/aaa/work/example/example_db.fasta

    ```

2. **Extract spectra files**

* Navigate to the `bin` folder of the pLink software directory.
* Command: **`sh pParse2Plus.sh "path_cfg.plink" "param_pparse" "param_plus"`**

  * In the command, `"path_cfg.plink"` is the full path to your `.plink` search parameter configuration file.
  * `"param_pparse"` and `"param_plus"`: the parameter strings for calling pParse and pParse2Plus, respectively. **Different MS data types require different `param_pparse` and `param_plus` values**:

    * MS2 with an **Orbitrap** analyzer:

        ```bash
        " -t -0.5 -z 5 -i 1 -C 1 -a 0 -p 1 -S 1 -b 1 -m 0" " -path:pParse2 ../pXtract3/pParse -Plus:pxtract_n_isotope 2"
        ```

    * MS2 with an **Astral** analyzer:

        ```bash
        " -t -0.5 -z 5 -i 1 -C 1 -a 0 -p 1 -S 1 -b 1 -m 0 -P 0" " -path:pParse2 ../pXtract3/pParse -Plus:pxtract_n_isotope 0"
        ```

    * **TimsTOF** instrument:

        ```bash
        " -t -0.5 -z 5 -i 1 -C 1 -a 0 -p 1 -S 1 -b 1 -m 0 -P 1 -F TIMS -n 1" " -path:pParse2 ../pXtract3/pParse -Plus:pxtract_n_isotope 0"
        ```
  * For example, a complete command for an Orbitrap analyzer:

    ```bash
    sh pParse2Plus.sh "/home/test_data/pLink_task.plink" " -t -0.5 -z 5 -i 1 -C 1 -a 0 -p 1 -S 1 -b 1 -m 0" " -path:pParse2 ../pXtract3/pParse -Plus:pxtract_n_isotope 2"
    ```
* **Spectra output**: binary spectral files in `.pf2` format will be generated in the same directory as the `RAW` files.

3. **Perform the search**

* Navigate to the `bin` folder of the pLink software directory.
* Command: **`sh MultiProcess.sh path_cfg.plink`**

  * In the command, `path_cfg.plink` is the full path to your `.plink` search parameter configuration file.

  * For example, a complete command:
    ```bash
    sh MultiProcess.sh /home/test_data/pLink_task.plink
    ```

* **Results output**: results will be generated in the folder specified by `result_output_path` in the `.plink` parameter file.


### Troubleshooting

#### Error: glibc version is too low

* **Issue description**

    ```
    /lib/x86_64-linux-gnu/libstdc++.so.6: version `GLIBCXX_3.4.30' not found (required by ./libsearch.so)
    ```

* **Solution**: upgrade the system glibc or or install glibc via Conda:

    ```bash
    conda create -n cpp_env -c conda-forge gcc_linux-64 gxx_linux-64
    conda activate cpp_env
    export LD_LIBRARY_PATH=$CONDA_PREFIX/lib:$LD_LIBRARY_PATH
    ```

### Reference information

* **Windows command-line usage**

  * [Running pLink via Command Line](https://github.com/pFindStudio/pLink3/wiki/Tips&Tricks#running-plink-via-command-line)
  * [Extracting Spectra via Command Line](https://github.com/pFindStudio/pLink3/wiki/Tips&Tricks#extracting-spectra-via-command-line)

---

## mzIdentML converter

### Usage

1. Download [pLink3-mzIdentML-converter](https://github.com/NorthblueM/pLink3-preview/releases).
2. Unzip the downloaded file and navigate to the folder containing `main.exe`.
3. Run: `main.exe -p path_cfg.plink -b path_plink_bin`

   * `path_cfg.plink`: full path to the `.plink` configuration file.
   * `path_plink_bin`: path to the `bin` folder of your pLink software installation directory.
   * **Default output**: `mzidentml-xml.mzid` in the `reports` folder inside the task results directory (i.e., the folder specified by `result_output_path` in the `.plink` file).
   * **Get help**: `main.exe -h`

* **Tips**:

  * The `.plink` configuration file is generated by the pLink GUI and is located in the task results folder.
  * The `.plink` file can be opened and edited with a text editor, such as Notepad++.
  
* **Note**:

  * Some cross-linkers or modifications may require manually adding their [unimod](https://unimod.org) `Accession number` to the configuration files `linker_accession.json` or `modification_accession.json`, if not already included.

### Reference information

* [mzIdentML Specification Document (PDF)](https://github.com/HUPO-PSI/mzIdentML/blob/master/specification_document/specdoc1_3/mzIdentML1.3.0-release.pdf)
* [mzIdentML v1.3 (HUPO-PSI)](https://www.psidev.info/mzidentml#mzid13)
* [mzIdentML reader](https://github.com/PRIDE-Archive/mzidentml-reader)

---

## ProXL converter
* [ProXL (Protein Cross-Linking Database): A Platform for Analysis, Visualization, and Sharing of Protein Cross-Linking Mass Spectrometry Data.](https://doi.org/10.1021/acs.jproteome.6b00274)
* Convert pLink3 cross-linking results to Proxl XML suitable for import into the ProXL web application.
* [Proxl Import Documentation](https://proxl-web-app.readthedocs.io/en/latest/using/upload_data.html)
* For more information about Proxl, visit [http://proxl-ms.org/](http://proxl-ms.org/).

### Usage

1. Download [pLink3-ProXL-converter](https://github.com/NorthblueM/pLink3-preview/releases).
2. Unzip the downloaded file and navigate to the folder containing `main.exe`.
3. Run: `main.exe -p path_cfg.plink -b path_plink_bin`

   * `path_cfg.plink`: full path to the `.plink` configuration file.
    * `path_plink_bin`: path to the `bin` folder of your pLink software installation directory.
   * **Default output**: `proxl-xml.xml` in the `reports` folder inside the task results directory (i.e., the folder specified by `result_output_path` in the `.plink` file).
   * **Get help**: `main.exe -h`

* **Tips**:

  * The `.plink` configuration file is generated by the pLink GUI and is located in the task results folder.
  * The `.plink` file can be opened and edited with a text editor (for example, Notepad++).

---

