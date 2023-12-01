# Installing

# Step 1: Install Visual Studio 2022

To start, you need to install Visual Studio 2022 on your Windows machine. This is required for the MSBuild based .NET Framework and Traditional DBL build systems. For .NET 6+ this is not required, but is still recommended for a better development experience

1. **Download Visual Studio 2022**: Visit the [official Visual Studio download page](https://visualstudio.microsoft.com/downloads/) and download the installer for Visual Studio 2022.

2. **Run the Installer**: Open the downloaded installer and proceed with the installation.

3. **Choose Workloads**: During the installation process, select the **.NET desktop development** workload. This workload includes necessary tools and libraries for DBL development.

#### Step 2: Download Synergy/DE Installers

Next, you need to download the Synergy/DE (SDE) installers, these provide command line tools for developing, compiling, linking, running and licensing your DBL applications.

1. **Visit Synergex Resource Center**: Go to [https://resources.synergex.com/SiteDownloads](https://resources.synergex.com/SiteDownloads).

2. **Log In**: Use your Resource Center account to log in. If you don’t have an account, you’ll need to contact synergex to get one.

3. **Download Installers**: Download the three latest Windows installers:
   - 64-bit SDE
   - 32-bit SDE
   - Visual Studio Integration (SDI)

#### Step 3: Install Synergy/DE

Now, install the Synergy/DE on your machine. Run the downloaded installers for both 64-bit and 32-bit SDE. Follow the on-screen instructions and pay close attention to step 4 below to complete the installations.

#### Step 4: Configure Licensing

During the installation of Synergy/DE, you’ll need to configure the licensing.

1. **License Configuration Prompt**: If this is the first time installing Synergy/DE on your machine, you will be prompted for license configuration.

2. **Select Licensing Type**:
   - Choose **License Server** or **Stand-alone (Local Licensing)** if you have a unique licensee name provided by Synergex.
   - Choose **License Client** if you need to specify the name of a license server.

3. **Enter License Details**:
   - For License Server/Stand-alone: Enter the unique licensee name given by Synergex.
   - For License Client: Enter the name of your license server.

   **Note**: If installing 32-bit Synergy/DE on a machine with 64-bit Synergy/DE network server licensing, the server name for the 32-bit installation defaults to the 64-bit machine’s name.

#### Step 5: Install Synergy/DE Visual Studio Integration

Now install the SDI (Synergy/DE Integration) for Visual Studio. This will integrate Synergy/DE with Visual Studio 2022.

#### Step 6: Verify Installation

After installation, it’s a good practice to verify that everything is set up correctly.

1. **Verify Synergy/DE Installation**: Open a command prompt and run `"%SYNERGYDE64%\dbl\dblvars64.bat" && lmu`. This should display the licensing information for your machine.
   
#### Conclusion

You have now successfully set up the DBL programming environment on your Windows machine using Visual Studio 2022. Now we can move on to creating our first DBL program.
