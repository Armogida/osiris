# Building Kit Definitions in OSIRIS - Step-by-Step Guide

## Overview

This guide provides step-by-step instructions for properly building new kit definitions in OSIRIS using Mac Terminal bash commands. It explains why some kit folders are not recognized by OSIRIS and how to ensure your kit definition is created correctly.

## Understanding the Problem

When you build a kit definition without specifying `SearchString` and `VolumeDirectoryName` parameters in your input file, OSIRIS creates a generic timestamp-based folder like `V-20251206-123000`. While this folder contains the necessary configuration files, **OSIRIS will not recognize it as a proper kit** because it lacks the SearchString that the application uses to associate the volume folder with a specific kit name.

### Why SearchString and VolumeDirectoryName Matter

- **SearchString**: Used by OSIRIS to identify and match the kit when reading data files. This string appears in the `<DirectorySearchString>` tag in the generated ladder file.
- **VolumeDirectoryName**: Specifies the exact name of the folder that will be created under `Config/Volumes/`. This is the folder OSIRIS looks for when loading kit configurations.

Without these parameters, your kit **will not appear in the kit selector** in OSIRIS.

## Prerequisites

Before you begin, ensure you have:

1. **Built OSIRIS** following the instructions in the main README.md
2. **Input files prepared**:
   - Bins file (e.g., `YourKit_Bins.txt`)
   - Panels file (e.g., `YourKit_Panels.txt`)
   - Ladder input file (e.g., `YourKitLadderInputFile.txt`)
3. **Access to the OSIRIS-Files directory** (typically in your home directory or a designated location)
4. **GenerateLadderFile tool** built and available in `osiris/GenerateLadderFile/bin/`
5. **BuildStandardControlFile tool** (optional, for standard control files) in `osiris/BuildStandardControlFile/bin/`

## Step-by-Step Instructions for Mac Terminal

### Example: Creating a Proper Kit Definition

Let's walk through creating a kit definition using a concrete example. We'll use a hypothetical kit called "PowerPlex35GY_Spectrum".

### Step 1: Prepare Your Workspace

```bash
# Navigate to your OSIRIS-Files directory (adjust path as needed)
cd ~/OSIRIS-Files

# Create a directory for your kit configuration files if it doesn't exist
mkdir -p ConfigurationTools/YourKitName
cd ConfigurationTools/YourKitName

# Place your Bins and Panels files here
# For example:
#   PowerPlex35GY_Bins.txt
#   PowerPlex35GY_Panels.txt
```

### Step 2: Create Your Ladder Input File

Create a file named `YourKitLadderInputFile.txt` with the following content. **This is the critical step** - make sure to include `SearchString` and `VolumeDirectoryName`:

```text
LadderOperation = New;
LadderFileName = PowerPlex35GY_Spectrum_LadderInfo.xml;
KitName = PowerPlex35GY Spectrum;
LadderDirectory = /Users/yourusername/OSIRIS-Files/ConfigurationTools/YourKitName;
NumberOfDyes = 5;
Version = 2.7;
Dye1 = FL;
Dye2 = JOE;
Dye3 = TMR-ET;
Dye4 = CXR-ET;
Dye5 = CC5;
Color1 = BLUE;
Color2 = GREEN;
Color3 = YELLOW;
Color4 = RED;
Color5 = ORANGE;
BinsFileName = PowerPlex35GY_Bins.txt;
PanelsFileName = PowerPlex35GY_Panels.txt;
SearchString = PowerPlex35GY_Spectrum;
VolumeDirectoryName = PowerPlex35GY_Spectrum;
OutputConfigPath = /Users/yourusername/OSIRIS-Files;
MaxExpectedAllelesPerLocusDefault = 2;
YLinkedDefault = false;
ILSFamilyName = PROMEGA-ILS-CC5-500-IDX;
ILSName = PROMEGA-ILS-CC5-500-IDX;
ILSChannel = 5;
StdControl = YourControlName;
HID = true;
;
```

**Important Notes:**
- **SearchString** and **VolumeDirectoryName** must have **NO SPACES**
- They should be descriptive and unique to your kit
- Replace paths with your actual paths
- The final line must be just a semicolon (`;`)
- Adjust dye names, colors, and other parameters to match your kit specifications

### Step 3: Run GenerateLadderFile

```bash
# Navigate to the GenerateLadderFile binary directory
cd ~/path/to/osiris/osiris/GenerateLadderFile/bin

# Copy your input file to this directory
cp ~/OSIRIS-Files/ConfigurationTools/YourKitName/YourKitLadderInputFile.txt ./LadderInputFile.txt

# Run the GenerateLadderFile command
./GenerateLadderFile

# Review the output for any errors
# The tool will create:
#   - Ladder specification file in OSIRIS-Files/Config/LadderSpecifications/
#   - Volume directory in OSIRIS-Files/Config/Volumes/PowerPlex35GY_Spectrum/
```

### Step 4: Verify the Kit Definition

```bash
# Check that the volume directory was created with the correct name
ls -la ~/OSIRIS-Files/Config/Volumes/

# You should see a directory named "PowerPlex35GY_Spectrum" (not "V-20251206-123000")

# Verify the contents of the volume directory
ls -la ~/OSIRIS-Files/Config/Volumes/PowerPlex35GY_Spectrum/

# You should see files like:
#   PowerPlex35GY_Spectrum_LabSettings.xml
#   PowerPlex35GY_Spectrum_StdSettings.xml
#   PowerPlex35GY_Spectrum_MessageBookV4.0.xml
#   PowerPlex35GY_Spectrum_access.txt

# Check that the ladder file was created
ls -la ~/OSIRIS-Files/Config/LadderSpecifications/

# You should see: PowerPlex35GY_Spectrum_LadderInfo.xml
```

### Step 5: Verify SearchString in Ladder File

```bash
# Open and check the ladder file contains the correct SearchString
grep "DirectorySearchString" ~/OSIRIS-Files/Config/LadderSpecifications/PowerPlex35GY_Spectrum_LadderInfo.xml

# Output should show:
#   <DirectorySearchString>PowerPlex35GY_Spectrum</DirectorySearchString>
```

### Step 6: Build Standard Control File (Optional)

If you need to create a standard control file:

```bash
# Navigate to BuildStandardControlFile binary directory
cd ~/path/to/osiris/osiris/BuildStandardControlFile/bin

# Run the tool with your OSIRIS-Files path
./BuildStandardControlFile "/Users/yourusername/OSIRIS-Files"
```

### Step 7: Test in OSIRIS

1. Launch OSIRIS application
2. Go to Tools → Lab Settings (or similar menu)
3. Check that your new kit appears in the kit selector dropdown
4. The kit should be listed as "PowerPlex35GY Spectrum" or the name you specified in `KitName`

## Fixing an Existing Generic Kit (e.g., V-20251206-123000)

If you already have a generic kit folder like `V-20251206-123000` and want to fix it:

### Option 1: Regenerate with Correct Parameters

1. Edit your original input file to add these lines:
   ```text
   SearchString = PowerPlex35GY_Spectrum;
   VolumeDirectoryName = PowerPlex35GY_Spectrum;
   ```

2. Delete the old generic folder:
   ```bash
   rm -rf ~/OSIRIS-Files/Config/Volumes/V-20251206-123000
   ```

3. Re-run GenerateLadderFile following Steps 3-7 above

### Option 2: Manual Rename and Update (Not Recommended)

This approach is more error-prone and requires manual XML editing:

1. Rename the directory:
   ```bash
   cd ~/OSIRIS-Files/Config/Volumes/
   mv V-20251206-123000 PowerPlex35GY_Spectrum
   ```

2. Rename all files inside:
   ```bash
   cd PowerPlex35GY_Spectrum
   for file in V-20251206-123000_*; do
     mv "$file" "PowerPlex35GY_Spectrum_${file#V-20251206-123000_}"
   done
   ```

3. Manually edit the ladder XML file to add/update the SearchString
4. Update references in Lab Settings files

**This manual approach is NOT recommended.** Use Option 1 instead to regenerate properly.

## Common Issues and Troubleshooting

### Issue: Kit doesn't appear in OSIRIS kit selector

**Causes:**
- Missing or incorrect `SearchString` in input file
- Missing or incorrect `VolumeDirectoryName` in input file
- Volume directory created with timestamp name (V-YYYYMMDD-HHMMSS)

**Solution:** Regenerate the kit definition following the steps above, ensuring both parameters are set correctly.

### Issue: GenerateLadderFile fails with "Search string is unspecified"

**Cause:** Missing `SearchString` parameter in input file

**Solution:** Add the following line to your input file (before the final semicolon):
```text
SearchString = YourKitName_NoSpaces;
```

### Issue: Files not found or wrong paths

**Cause:** Incorrect paths in input file

**Solution:** Use absolute paths in your input file. Verify all paths exist:
```bash
# Check your ladder directory
ls -la /Users/yourusername/OSIRIS-Files/ConfigurationTools/YourKitName

# Check your output config path
ls -la /Users/yourusername/OSIRIS-Files
```

### Issue: Permission denied errors

**Cause:** Insufficient permissions to create directories

**Solution:** Ensure you have write permissions:
```bash
chmod -R u+w ~/OSIRIS-Files
```

## Quick Reference: Required Input File Parameters

Minimal required parameters for a proper kit definition:

```text
LadderOperation = New;
LadderFileName = YourKit_LadderInfo.xml;
KitName = Your Kit Name;
LadderDirectory = /full/path/to/bins/and/panels;
NumberOfDyes = 5;
BinsFileName = YourKit_Bins.txt;
PanelsFileName = YourKit_Panels.txt;
SearchString = YourKitName_NoSpaces;              # REQUIRED - no spaces!
VolumeDirectoryName = YourKitName_NoSpaces;       # REQUIRED - no spaces!
OutputConfigPath = /full/path/to/OSIRIS-Files;
ILSChannel = 5;
ILSFamilyName = Your_ILS_Family;
ILSName = Your_ILS_Name;
StdControl = YourControlName;
;
```

## Additional Resources

- See example input files in `osiris/ConfigurationTools/DemoFiles/`
- Tutorial example: `osiris/ConfigurationTools/DemoFiles/PPFusion_Tutorial_input_demo.txt`
- Existing kit configurations in: `osiris/GenerateLadderFile/*/LadderInputFile.txt`
- Documentation: `osiris/Documentation/Adding a New Kit to Osiris.docx`

## Summary

To ensure OSIRIS recognizes your kit definition:

1. ✅ **Always include** `SearchString` parameter (no spaces)
2. ✅ **Always include** `VolumeDirectoryName` parameter (no spaces)
3. ✅ Use descriptive, unique names that identify your kit
4. ✅ Verify the volume directory is created with the correct name (not V-timestamp)
5. ✅ Test that the kit appears in OSIRIS kit selector

Following these guidelines will ensure your kit definition is properly recognized and usable in OSIRIS.
