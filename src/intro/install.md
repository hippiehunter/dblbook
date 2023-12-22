# Installation

`**Brief intro statement here?`

## Step 1: Install Visual Studio 2022

To start, you need to install Visual Studio 2022 on a Windows machine. This is required for the MSBuild-based .NET Framework and Traditional DBL build systems. When developing for .NET 6+, this is not required but is still recommended for a better development experience.

`**you'll also need certain versions of .NET Framework, so mention that here, or point readers to requirements doc?`

1. **Download Visual Studio 2022**: Visit the [official Visual Studio download page](https://visualstudio.microsoft.com/downloads/) and download the installer for Visual Studio 2022.

2. **Run the installer**: Open the downloaded installer and proceed with the installation.

3. **Choose workloads**: During the installation process, select the **.NET desktop development** workload. This workload includes necessary tools and libraries for DBL development.
`**How about ".NET Cross-Platform Development"? The SDI Requirements page says it's needed for .NET 6+ development.`

## Step 2: Download Synergy/DE installers

Next, you need to download the Synergy/DE (SDE) installers. These provide command line tools for developing, compiling, linking, running, and licensing your DBL applications.

1. **Visit Synergex Resource Center**: Go to [https://resources.synergex.com/SiteDownloads](https://resources.synergex.com/SiteDownloads).

2. **Log in**: Use your Resource Center account to log in. If you don’t have an account, you’ll need to contact Synergex to get one.

3. **Download installers**: Download the three latest Windows installers:
   - Synergy/DE 64-bit (104)
   - Synergy/DE 32-bit (101)
   - SDI 32-bit (101) 

## Step 3: Install Synergy/DE

Now, install Synergy/DE on your machine. First run the downloaded Synergy/DE 64-bit (104) installer, and then run the Synergy/DE 32-bit (101) installer. Follow the on-screen instructions and pay close attention to step 4 below to complete the installations.

## Step 4: Configure licensing

During the installation of Synergy/DE, you’ll need to configure the licensing.

1. **License configuration prompt**: If this is the first time Synergy/DE has been installed on your machine, you will be prompted for license configuration.

2. **Select licensing type**:
   - Choose **License Server** or **Stand-alone (Local Licensing)** if you have a unique licensee name provided by Synergex.
   - Choose **License Client** if you need to specify the name of a license server.

3. **Enter license details**:
   - For License Server/Stand-alone: Enter the unique licensee name given by Synergex.
   - For License Client: Enter the name of your license server.

   **Note**: If installing 32-bit Synergy/DE on a machine with 64-bit Synergy/DE network server licensing, the server name for the 32-bit installation defaults to the 64-bit machine’s name.

## Step 5: Install Synergy/DE Visual Studio Integration

Now install Synergy/DE Integration for Visual Studio (SDI) by running the SDI 32-bit (101) installer. This will integrate Synergy/DE with Visual Studio 2022.

## Step 6: Verify the installation

After installation, it’s a good practice to verify that everything is set up correctly. 

1. **Verify licensing**: Open a command prompt and run `"%SYNERGYDE64%\dbl\dblvars64.bat" && lmu`. This should display the licensing information for your machine.
   
## Conclusion

You have now successfully set up the DBL programming environment on your Windows machine using Visual Studio 2022. In the future when someone says to get a "DBL command prompt" or to ensure DBL environment variables have been configured in your command prompt, you can just run `"%SYNERGYDE64\dbl\dblvars64.bat"`, that will bring in the environment variables required in order to build/run DBL code in 64bit. If you need the 32bit version instead you can just switch the 64 parts in that command and run `"%SYNERGYDE32\dbl\dblvars32.bat"`. Now we can move on to creating our first DBL program.

