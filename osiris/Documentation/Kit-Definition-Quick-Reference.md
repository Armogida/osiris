# OSIRIS Kit Definition Quick Reference

## Why Your Kit Doesn't Appear in OSIRIS

If your kit folder is named like `V-20251206-123000`, it **will NOT appear** in OSIRIS kit selector because it lacks a `SearchString`.

## The Solution (3 Steps)

### 1. Edit Your Input File

Add these two lines to your ladder input file (before the final `;`):

```text
SearchString = YourKitName_NoSpaces;
VolumeDirectoryName = YourKitName_NoSpaces;
```

**Important:** No spaces allowed in these names!

### 2. Delete Old Folder

```bash
# First, list to verify which folder(s) will be deleted
ls ~/OSIRIS-Files/Config/Volumes/V-*

# If the correct folder is shown, delete it
# Replace V-YYYYMMDD-HHMMSS with your actual timestamp folder name
rm -rf ~/OSIRIS-Files/Config/Volumes/V-YYYYMMDD-HHMMSS
```

### 3. Regenerate

```bash
cd ~/path/to/osiris/osiris/GenerateLadderFile/bin
cp ~/your/input/file.txt ./LadderInputFile.txt
./GenerateLadderFile
```

## Verify Success

```bash
# Should see YOUR kit name, not V-timestamp
ls ~/OSIRIS-Files/Config/Volumes/

# Should show your SearchString
grep "DirectorySearchString" ~/OSIRIS-Files/Config/LadderSpecifications/*.xml
```

## Complete Example Input File

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
SearchString = PowerPlex35GY_Spectrum;           ← REQUIRED!
VolumeDirectoryName = PowerPlex35GY_Spectrum;    ← REQUIRED!
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

## For Complete Details

See: [Building Kit Definitions Guide](Building-Kit-Definitions-Guide.md)
