# ParaView Template
ParaView directory include all files required for integrating openfoam 6 Paraview with IBM Spectrum LSF and IBM Spectrum LSF Application Center. Users can manage openfoam data in paraview console inside Spectrum LSF application Center.
This integration is based on public docker image: openfoam/openfoam6-paraview54, there is no need to install openfoam application.

## Prerequisites
1). IBM Spectrum LSF 10.1.0.9 or above version is installed.

2). IBM Spectrum Application Center 10.2.0.9 or above version is installed.

3). LSF Compute Server support docker engine 1.12 or above version.

4). OpenFOAM template is installed into Application Center

## Deployment Steps
Step 1: download all the files under this directory ParaView/,   and copy over to IBM Spectrum LSF Application Center configuration 
        directory, for example:  /opt/ibm/lsfsuite/ext/gui/conf/application/draft/ParaView, make sure files owner are administrator, 
        all files have excutable permission.
        
Step 2: Configure LSF to define LSF Resource "ParaView" and assign the available Hosts to this resource.
   
  1). define LSF resource "ParaView"-- add a line into $LSF_ENVDIR/lsf.shared inside "Begine Resource" and "End Resource":
	
	ParaView   Boolean ()       ()          (openfoam GUI)
        
  2). define ParaView running hosts-- edit file $LSF_ENVDIR/lsf.cluster.CLUSTER_NAME, add resource name for selected hosts:
	
	ib22b04 !       !       1       (docker ParaView)
	
  3). Enable lsrun function in $LSF_ENDIR/lsf.conf:
  
        LSF_DISABLE_LSRUN=N 
	
Step 3: Prepare hosts to run TigerVnc.  For Application center host and all hosts selected in Step 3, run the following steps for RHEL 7:
   
    yum -y install tigervnc*
    yum group install "Server with GUI"
    yum group install "X Window System"
    systemctl set-default graphical.target
    yum install gnome-screensaver*
      
   for more details, refer to [Preparing Linux IBM Spectrum LSF Application Center servers to remotely launch](https://www.ibm.com/support/knowledgecenter/en/SSZRJV_10.2.0/admin_guide/remote_vncserver_config.html)
  

Step 4: restart LSF:   
        
	lsfrestart
        
Step 5: logon LSF Application Center as administrator,  find and edit the template "ParaView", replace JOB_REPOSITORY_TOP with the real 
        directory introduced in Step 2 for input: "Application Command" and "Application Command to open an input file",  save and 
        publish the template.  Go to "System&Setting"-> User "Role& Permission", assign view and control permission of template 
	ParaView to "Normal User"

Step 6: Test-- open a new tab in browser used in step6, type URL: https://<AC_SERVER>:6080/, accept this site. then run cases:

Case 1:  logon Application Center as a normal user, go to "+ New Workload", click on "Paraview",  a new browser window
        should show up with Paraview console running inside.
	
Case 2: logon Application Center as a normal user, submit a OpenFOAM job,  in the workload list, click on the job ID to open
        this job's details, click on "Data" tab, search and select  XXX.foam file, then click on the menu: Open with application, select
        ParaView.   an new browser window should be opened, and Paraview console is running with the current job's data opened.
