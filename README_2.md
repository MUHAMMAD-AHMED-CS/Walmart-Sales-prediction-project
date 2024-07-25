
# SWCs requirement and Calibration Repository Script (calibration_req.sh)

The first part of the script is designed to download and copy the requirements.zip file to specified hosts to run SWCs on both SoCs. the second part of the script is designed to clone a specified branch from the calibration repository and copy the cloned repository to specified hosts SoCs.

## Prerequisites

- Ensure `sshpass` is installed on your system.
- Ensure `git` is installed on your system.
- Have access to the repository at `https://gitlab.sw-motion.cn/jac-soc/calibrations.git`.
- Ensure SSH access to the hosts `192.168.3.11` and `192.168.3.10`.

## Usage

### Part-1:
The script can be used to:
1. Download the requirements.zip form the specified scouce.
2. unzip the requirements.zip.
3. Copy the content in the requirements.zip to designated hosts in this case SOC_A and SOC_B.

### Part-2:
The script can be used to:
1. Fetch and display a list of available branches from the repository.
2. Clone a specified branch from the repository.
3. Copy the cloned repository to designated hosts.


Make sure the script is executable:

```bash
chmod +x calibartion_req.sh
```
You can run the script by passing a branch name as an argument, or without any arguments to be prompted for a branch name.

#### With a Branch Name

```bash
./calibartion_req.sh <branch_name>
```
#### Example:

```bash
./calibartion_req.sh main
```
#### Without a Branch Name
```bash
./calibartion_req.sh
```
If no branch name is provided, the script will fetch and display a list of available branches from the repository and prompt you to enter the branch name you want to clone.


## Script Details Part-1 for requirements.zip downloading and copying on the SOCs.

### Variables
- URL: Placeholder for the URL to download the required file (commented out in this script).
- file_name: The name of the file to be downloaded (commented out in this script).
- extracted_dir: The directory where the downloaded file will be extracted (commented out in this script).


### Downloading the File (Commented Out)

```bash

 Download the file
 curl -L -o $file_name $URL
 if [ $? -ne 0 ]; then
     echo "Error: Failed to download the file from $URL"
     exit 1
 fi

 Unzip the file
 mkdir -p $extracted_dir
 unzip $file_name -d $extracted_dir
 if [ $? -ne 0 ]; then
     echo "Error: Failed to unzip the file"
     exit 1
 fi

```
### Copying the Files
The script copies the opencv.hpp file to two specified hosts using sshpass:

```bash
echo "copying the folders into the Host A_office"
sshpass -p "Huawei12#$" scp -v -r ./opencv.hpp root@192.168.3.10:/home/mdc/external_deps
if [ $? -ne 0 ]; then
    echo "Error: Failed to copy folders to the board"
    exit 1
fi

echo "copying the folders into the Host B_office"
sshpass -p "Huawei12#$" scp -v -r ./opencv.hpp root@192.168.3.11:/home/mdc/external_deps
if [ $? -ne 0 ]; then
    echo "Error: Failed to copy folders to the board"
    exit 1
fi
Error Handling
If any scp command fails, the script will print an error message and exit with a status code of 1.
```


## Script Details Part-2 for cloning calibration repository from the desired branch and copying the content on the SOCs
Fetch and Display Branch List
The function fetch_branch_list fetches and displays the list of branches available in the repository:

```bash
fetch_branch_list() {
    echo "Fetching branch list..."
    git ls-remote --heads $repository_url | awk -F '/' '{print $NF}'
}
```
### Cloning a Branch
If a branch name is provided, the script clones that specific branch into a folder named after the repository:

```bash
destination_folder=$(basename $repository_url .git)
echo "Cloning branch '$branch_name' into $destination_folder..."
git clone -b $branch_name --single-branch $repository_url $destination_folder
```
If cloning is successful, it prints a success message; otherwise, it prints an error message.

### Copying the Cloned Repository
The script copies the cloned repository to the specified hosts using sshpass:

```bash
sshpass -p "Huawei12#$" scp -r $destination_folder root@192.168.3.11:/home/mdc/external_deps
sshpass -p "Huawei12#$" scp -r $destination_folder root@192.168.3.10:/home/mdc/external_deps
```
If copying fails, an error message is printed, and the script exits with a status code of 1.
