# Convert trajectory files to smaller trajectory file for viewer
## Convert using Gromacs
- [ ] gmx trjconv -f step7.trr -s step6.0_minimization.tpr -o PnuC_0NR_1us.trr -pbc cluster -dt 1000

*Choose “Protein” for cluster and “whole system” for output 

## How to load vmd on my Linux and view the trajectory file

- [ ] cd /home/dout2/Application/vmd_bin
- [ ] ./vmd

## Convert .trr to .pdb file for Pymol view
- [ ] gmx trjconv -f step7.trr -s step6.0_minimization.tpr -o PnuC_0NR_1us.pdb -pbc nojump -dt 1000

*Choose “Protein” for output




# How to view CG simulationr results using VMD: 

*Reference/From: http://md.chem.rug.nl/index.php/tools2/visualization

Visualization

Rendering CG bonds & constraints with VMD

Last Updated: Wednesday, 16 August 2017 09:24

```sh
We modified and re-wrote (check this page) a small Tcl script to read and draw Martini bonds and constraints in VMD. The bonds of the elastic networks can be drawn as well. In some cases, the script needs to read the gromacs .tpr file. For this reason, there is a latest version for use with gromacs 5 (and later) .tpr files.

The script for use with gromacs 5 can be found here: cg_bonds-v5.tcl.

The script can be found here: cg_bonds.tcl (previous version: cg_bonds.tcl.old).
```
 
# Usage
The script is invoked from the VMD command line - after sourcing it (source /path/to/cg_bonds.tcl, or /path/to/cg_bonds-v5.tcl, simply type cg_bonds (options and default values listed below). You can summon an "how to" at any moment by calling the cg_bonds_usage command.

This script uses the gmxdump executable compiled with gromacs. By default, it looks for it in the /usr/bin directory; you can specify another path with a specific option (see below). The script can now parse .top and .itp files, via the -top option.

# cg_bonds



Options and default values (slightly different from the previous version!):

- -molid	"top"	VMD-defined ID of the molecule to process
- -gmx	/usr/bin/gmxdump	absolute path to gmxdump executable (for version 5, this should point to the gmx executable, default /usr/bin/gmx)
- -tpr	topol.tpr	absolute path to the simulation file (.tpr)
- -top	topol.top	absolute path to the system topology files (.top linking to .itp)
- -topoltype	"martini"	protein topology type: "martini", "elastic" or "elnedyn"
- -net	"martini"	network to draw: "martini", "elastic" or "both"
- -bndrcnstr	"both"	draw bonds AND/OR constraints "bonds", "constraints" or "both"
- -cutoff	7.0	cutoff for bonds (angstroms)
- -color	"red"	color (color name or VMD-defined ID) of elastic bonds
- -mat	"Opaque"	material for elnedyn bonds
- -rad	0.2	radius of elastic bonds
- -res	6	resolution of elastic bonds

In most of the cases, if a "classical" cut-off is used for the elastic network (0.9nm), more than 12 bonds per atom are defined and VMD refuses to draw them. BUT this script replaces bonds by cylinders, and is able to draw the entire elastic network. Note that you have to modify the default cutoff value to see all the links defined by the elastic network.

Then you can remove Martini bonds with the cg_delete_martini_bonds; and the cylinders with the cg_delete_elastic_bonds. The only option for the previous three commands is the VMD-defined ID of the molecule to process (via -molid, default is "top").

# Known issues

Using tpr files from version 5 and higher is not possible.Script for use with version 5 and higher is now available!
The cg_bonds_infos command is useless (and going to be removed in next versions probably). Removed!
Bonds drawn for the elastic network don't follow the dynamic of the protein; the 12-bonds-limitation of VMD obliges to draw them using cylinders, not regular bonds. Please drop a mail if you have a solution to fix that...
We need a compiled version of gromacs around and the gmxdump executable to extract bonds/constraints (which takes time); wait for the next version! It will certainly read .top and .itp files.Added! The previous method (gmxdump) is still working; and watch out, flags have slightly changed.
The table given in output (and the script in general) is not totally reliable in some cases; hand written topologies for examples...Removed.
According to this topic (thanks Kargar), it seems the version of VMD should be superior to 1.8.5 (excluded) to run this script properly. The current version of VMD is 1.9 (august 2011).
Not really an issue but: cg_bonds draws ALL bonds/constraints of the system; what if we want toRender the secondary structureand draw them only for some residues?Well... Use the good representation.
Tcl needs an empty line at the end of the files it reads (.top and .itp)!
On the same level, Tcl seems to understand differently the simple and double quotes... Please check that before shouting.
Script doesn't extract (yet!) harmonic bonds of "regular" elastic networks built with the martinize.py script for instance (elastic bonds defined as type 6; this comment is not valid for ElNeDyn, which defines elastic bonds as type 1).Fixed!


# Example

```sh
user@machine $ vmd protein.gro
vmd > source /home/user/scripts/cg_bonds.tcl
vmd > cg_bonds -top system.top -topoltype "elastic"
vmd > cg_bonds -gmx /home/user/bin/gmx-4.5.4/bin/gmxdump -tpr dyn.tpr -net "elastic" -cutoff 12.0 -color "orange" -mat "AOChalky" -res 12 -rad 0.1 -topoltype "elastic"
vmd > cg_delete_elastic_bonds
```

The few lines above will:
```sh
load the protein.gro structure,
source the cg_bonds.tcl script,
draw the martini bonds (using the .top file specified in the command line) extracted from a .top containingelastic network bonds,
draw the elastic network with the options (cutoff, color, material, resolution and radius) specified by extracting the bonds from the .tpr file,
delete the elastic network bonds previously drawn.
```
All these line will be applied to the molecule with the VMD-defined ID 0 (or "top").

For feedback or problems contact c.arnarez@rug.nl or post to the platform.

Rendering CG helices & sheets with VMD
Last Updated: Thursday, 12 June 2014 15:31
We gathered and extended the set of small Tcl scripts available in our group to draw protein secondary structure in VMD-like fashion.

# The script can be found here: cg_secondary_structure.tcl

## Usage

After sourcing the script in VMD (source /path/to/cg_secondary_structure.tcl), two routines are available from the command line: cg_helix and cg_sheet. The options and default values are listed below. You can summon an "how to" at any moment by calling the cg_secondary_structure_usage command.

This script needs the La package, a linear algebra package written by Hume Integration Software, used here to compute principal axes of helices/sheets; the package is freely available at http://www.hume.com/la/. After downloading it, extract and source the la.tcl file in VMD.

You can give an output of do_dssp as an input for this script; it will be parsed and this script will try to draw each helix/sheet. If you use this option (-ssdump), you can leave the first list empty (see examples).

# cg_helix

## Usage:

vmd > cg_helix {{start1 end1} {start2 end2} ...} [OPTIONS]
Where start and end are respectively the first and last residues composing an helix; you can provide more than one helix, as shown in the command line. The "{" and "}" characters are mandatory for Tcl to understand it as a list!

Options and default values:

-molid	"top"	VMD-defined ID of the molecule to process
-ssdump	"ssdump.dat"	read topology from a do_dssp-formated file
-bbbname	"B.*"	backbone bead name
 	 	 
-hlxmethod	"idealhelix"	method to draw sheets "idealhelix", "realhelix" or "cylinder"
-hlxcolor	"red"	color of helices
-hlxmat	"Opaque"	material
-hlxres	12	resolution
-hlxrad	2.0	radius of cylinders
-hlxsides	"round"	arrow sides "round" or "sharp"
-hlxfilled	"yes"	cylinders filled or not "yes" or "no"
 	 	 
-hlxstep	1.0	angle step size
-hlxthick	0.2	thickness of helix
-hlxwidth	2.0	width of helix
-hlxdirect	"no"	draw the director vector of the helix (or not)
-hlxdirrad	0.1	radius of the cylinder (director)
-hlxdirclen	0.3	length of the cone (showing direction of the director)
-hlxdircrad	0.2	cone radius
-hlxsecbprop	0.5	proportion of the preceding/following bond used as length for starting/ending flat cones

Example:
```sh
vmd > cg_helix {{5 48} {120 146}} -hlxmethod "cylinder" -hlxcolor "lime" -hlxrad 2.5
```

# cg_sheet

## Usage:

vmd > cg_sheet {{start1 end1} {start2 end2} ...} [OPTIONS]
Where start and end are respectively the first and last residues composing a sheet; you can provide more than one sheet, as shown in the command line. The "{" and "}" characters are mandatory for Tcl to understand it as a list!

Options and default values:

-molid	"top"	VMD-defined ID of the molecule to process
-ssdump	"ssdump.dat"	read topology from a do_dssp-formated file
-bbbname	"B.*"	backbone bead name
 	 	 
-shtmethod	"flatarrow"	method to draw sheets "cylindarrow", "flatarrow", "bendedarrow" or "triangle"
-shtcolor	"green"	color of sheets
-shtmat	"Opaque"	material
-shtres	12	resolution
-shtsides	"round"	sheet sides "round" or "sharp"
 	 	 
-shtrad	0.4	radius of cylinders
-shtconrad	0.8	radius of arrow cones
-shtlencone	1.5	length of arrow cones
-shtfilled	"yes"	cylinder filled or not "yes" or no"
 	 	 
-shtarrwidth	2.0	width of arrows
-shtheadsize	4.0	size of the arrow heads
-shtarrthick	1.0	thickness of arrows
 	 	 
-shtstep	0.1	distance step size
-shtthick	0.2	thickness of sheet
-shtwidth	2.0	width of sheet
-shtdirect	"no"	draw the director vector of the sheet (or not)
-shtdirrad	0.1	radius of the cylinder (director)
-shtdirclen	0.3	length of the cone (showing direction of the director)
-shtdircrad	0.2	cone radius
-shtsecbprop	0.5	proportion of the preceding/following bond used as length for starting/ending flat cones

Example:
```sh
vmd > cg_sheet {} -shtfilled "no" -ssdump protein.dat -shtarrowthick 0.4
```
Known issues

There is still a bunch of small graphical issues (thanks to drop a mail to the contact at the bottom of this page whenever you find something... unexpected); sheets are particularly ugly. It's not the priority right now, it will be fixed when we will have time... keep checking this page and your version of the script updated! We are writing this tool for personal purpose, and we didn't retroactively test all the graphical options and the result when mixing them together. Thanks to do that for us!

Drawings do not follow the protein dyamic along a trajectory; you need to redraw everything at each step (no ways to change this behavior so far).
The "bendedarrow" (sheet style), which would be the closest to VMD atomistic drawings, doesn't work so well so far. Still working on it!
If an helix is too bended, VMD will not be able to draw it using the "idealhelix" or "realhelix" method (the script will output an error message with the termini of the helix).

Not able to draw helices smaller than 3 residues (!).Fixed. Avoided.

Tcl needs an empty line at the end of the files it reads!

On the same level, Tcl seems to understand differently simple and double quotes... Please check that before shouting.

For feedback or problems contact c.arnarez@rug.nl or post to the platform.


