

# Background

Reading crystallographic models as `.cif` file,
CrystalExplorer<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup> is capable to assess the electronic
interactions of a molecule with its surrounding crystal.  This is
visualized with Hirshfeld surfaces,<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup> and may be projected as a
2D fingerprint map.<sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup>

Carter *et al.* suggest the inspection of *the differences* of these
2D fingerprint maps<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup> to ease discern of similarities and
differences when comparing these fingerprints: Contrasting to a
qualitative discern of the maps (e.g. `compare` by
ImageMagick),<sup><a id="fnr.5" class="footref" href="#fn.5">5</a></sup> this approach allows both a visual as well as
numeric quantification of the differences.  This is illustrated
below with the example of two crystallographic model data describing
two polymorphs of benzamide (fig. [3](#org7506e6a)).  The scope of this
analysis may be extended to isostructural materials, too.

![img](./alignment.png "2D fingerprint maps of Hirshfeld surfaces for CCDC model BZAMID01 and BZAMID11 (left and left center), derived from the analysis by CrystalExplorer at *very high* resolution (d<sub>e</sub> and d<sub>i</sub> in the range of 0.40(0.01)3.00 &Aring;).  Qualitative difference assignment by superposition as provided by ImageMagick (right center).  Simultaneous qualitative and quantitative spatial information provided by the *difference map* generated with the scripts of this repository.")


# Basic use

The following sequence describes the analysis of the two benzamide
model data on Linux Xubuntu 18.04.3LTS, CrystalExplorer
(version 17.5) and the scripts of this repository.  The results were
visualized with `gnuplot` (version 5.2.7beta).

-   By default CrystalExplorer removes intermediate working files,
    including the `.csx` this analysis requires.  To retain this
    information, access the preferences menu (File -> Preferences) and
    uncheck the corresponding entry located under the expert tab. Now
    close the preference menu and load the `.cif` file in question.
    Compute the Hirshfeld surface at *very high* resolution (i.e., one
    level higher than by default).
    
    Starting with `example.cif`, CrystalExplorer provides you
    `example_example.csx`.  For convenience of file management, it is
    recommended to shorten the file name to `example.csx`.

-   Compile the Fortran sourcecode, for example with the freely
    available `gfortran`.<sup><a id="fnr.6" class="footref" href="#fn.6">6</a></sup> The then obtained executable is used
    to normalize CrystalExplorer’s output (the `.csx` file), exported
    as a `.dat` file. Similar to CrystalExplorer, you opt for either
    one of the following three map ranges to be considered for output:
    
    -   standard map: d<sub>i</sub> and d<sub>e</sub> in the range of 0.40&#x2026;2.60 &Aring;
        (221 bins in each dimension),
    
    -   translated map: d<sub>i</sub> and d<sub>e</sub> in the range of 0.80&#x2026;3.00 &Aring;
        (221 bins in each dimension), or
    
    -   extended map: d<sub>i</sub> and d<sub>e</sub> in the range of 0.40&#x2026;3.00 &Aring;
        (261 bins in each dimension)
    
    Independent of your choice, since CrystalExplorer's computation
    was set to *very high* resolution, the increment into each
    direction is 0.01 &Aring;. For and initial survey, the extended map
    range is the most suitable.
    
    On the CLI, your instructions follow the pattern of:
    
        gfortran fingerprint.F90 -o fingerprint.x    # compilation
        ./fingerprint.x in.cxs [standard | translated | extended] out.dat # your options
        ./fingerprint input.cxs standard output.dat  # usage of the executable

-   The difference between two normalized Hirshfeld surface 2D
    fingerprints, for example `map_A.dat` and `map_B.dat` is assessed
    with the C program `diff_finger.c`.  After compilation of an
    executable, e.g. with the freely available `gcc`,<sup><a id="fnr.7" class="footref" href="#fn.7">7</a></sup> indicate
    the two files to compare with each other and the file to write the
    results to.
    
    On the CLI, your instructions follow the pattern of:
    
        gcc diff_finger.c -o diff_finger  # compilation
        ./diff_finger map_A.dat map_B.dat > diff_map_A_map_B.dat  # application
    
    Since only maps of same type (standard, translated, extended)
    should be compared with each other, it may be helpful to name the
    `.dat` files accordingly.

-   Ruby script `sum_abs_diffs.rb` assigns an absolute value by
    "summing up the absolute value of each point in the difference
    plot between normalized 2D fingerplots [when] compared with each
    other."  It is launched from the CLI with the difference map data
    just obtained as sole parameter:
    
        ruby sum_abs_diffs.rb example.dat             # general pattern
        sum_abs_diffs.rb diff_BZAMID01_BZAMID11.dat   # example input
        >>> diff_BZAMID01_BZAMID11.dat  61.4040       # example output

-   Aiming for a visual representation, sub-folder `plot2gnuplot`
    contains two sets of auxiliary bash scripts to relay the results
    stored in `.dat` files to the freely available `gnuplot`<sup><a id="fnr.8" class="footref" href="#fn.8">8</a></sup> in
    either bitmap (.png) or vector (.pdf) format.<sup><a id="fnr.9" class="footref" href="#fn.9">9</a></sup> If functional
    
    -   *individual* normalized 2D fingerprint maps may be generated
        with scripts like `plot_finger.sh`.  The color scheme is the one
        already introduced by CrystalExplorer.
    
    -   The *difference* of two normalized 2D fingerprint maps may be
        plot with scripts like `plot_diff_finger.sh`.  The color scheme
        deploys a blue-white-red scale.
    
    To save file size, scripts including the `sparse` string in the
    file name consider tiles to be put into the diagrams only if *i*)
    they either represent a *z*-value different from zero, or *ii*)
    are surrounded both in d<sub>i</sub> and d<sub>e</sub> direction by other tiles with a
    non-zero *z*-value.  By virtue of this 'conditional
    plotting',<sup><a id="fnr.10" class="footref" href="#fn.10">10</a></sup> the overall processing time per file equally
    decreases.
    
    The `.sh` scripts *may* require 'executable bit' prior to their
    deployment.  This may be adjusted at a local level without
    administrator privileges.  A local `readme` in the sub-folder
    provides additional information about them.
    
    Hence, your instructions on the CLI follow the pattern of:
    
        chmod u+x plot_finger.sh           # provision of the executable bit
        ./plot_finger.sh example.dat       # plot of a 2D fingerprint map
        ./plot_diff_finger.sh example.dat  # plot the difference fingerprint map
    
    `END`


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> <http://crystalexplorer.scb.uwa.edu.au/>

<sup><a id="fn.2" href="#fnr.2">2</a></sup> a) "A novel definition of a molecule in a crystal", Spackman,
M. A.; Byrom, P. G. in Chem. Phys. Lett., 1997, 267, 215&#x2013;220, doi
[10.1016/S0009-2614(97)00100-0](https://www.sciencedirect.com/science/article/pii/S0009261497001000?via%3Dihub). b) "Novel tools for visualizing and
exploring intermolecular interactions in molecular crystals",
McKinnon, J. J.; Spackman, M. A.; Mitchell, A. S. in Acta Cryst. B,
2004, 60, 627&#x2013; 668, doi [10.1107/S0108768104020300](http://scripts.iucr.org/cgi-bin/paper?S0108768104020300). c)
<http://130.95.176.70/wiki/index.php/The_Hirshfeld_Surface>

<sup><a id="fn.3" href="#fnr.3">3</a></sup> "Fingerprinting Intermolecular Interactions in Molecular
Crystals", Spackman, M. A.; McKinnon, J. J. in CrystEngComm, 2002, 4,
378&#x2013;392, doi [10.1039/B203191B](https://pubs.rsc.org/en/content/articlelanding/2002/ce/b203191b#!divAbstract).

<sup><a id="fn.4" href="#fnr.4">4</a></sup> "Difference Hirshfeld fingerprint plots: a tool for studying
polymorphs." Carter, D. J.; Raiteri, P.; Barnard, K. R.; Gielink, R.;
Mocerino, M.; Skelton, B. W.; Vaughan, J. G.; Ogden, M. I.; Rohl,
A. L. in CrystEngComm, 2017, 19, 2207&#x2013;2215, DOI: [10.1039/c6ce02535h](https://pubs.rsc.org/en/content/articlelanding/2017/ce/c6ce02535h#!divAbstract).

<sup><a id="fn.5" href="#fnr.5">5</a></sup> <https://imagemagick.org/> Within the bundle, the instruction
following the basic pattern of `compare image_A image_B` provides a
check.  Additional information on
<https://imagemagick.org/script/compare.php>.

<sup><a id="fn.6" href="#fnr.6">6</a></sup> The script successfully works with gfortran (version 7.4.0).
The optimization of the executable (`-O` parameter) does not offer a
noticeable advantage if comparing a few model data.

<sup><a id="fn.7" href="#fnr.7">7</a></sup> In preparation of this how-to, gcc in 7.4.0 was used
successfully.

<sup><a id="fn.8" href="#fnr.8">8</a></sup> <http://gnuplot.info>

<sup><a id="fn.9" href="#fnr.9">9</a></sup> Note that if you would like to use these bash `.sh` scripts as
gnuplot `.plt` files, occasionally instructions like `\$` (with
backslash) escaping the shell need to be reset as `$` (without
backslash).

<sup><a id="fn.10" href="#fnr.10">10</a></sup> Thanks to Ethan Merrit who suggested this additional
improvement in a private communication.  Savings in time and file
volume are especially noticed for the generation of .pdf.
