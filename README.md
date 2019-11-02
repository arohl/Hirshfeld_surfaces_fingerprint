

# Background

While reading crystallographic models as `.cif` file,
CrystalExplorer is capable to visualize intermolecular interactions
by Hirshfeld surfaces, and subsequently project these as a 2D
fingerprint map.  As intermolecular interactions differ among
polymorphs, Carter *et al.* suggest the inspection of the
differences of these 2D fingerprint maps as aide to recognize
differences more easily.  Contrasting to a qualitative discern of
the maps (e.g. by `compare` provided by ImageMagick), these scripts
allow a quantification of the differences, too.


# Basic use

The following sequence allows you to compute difference Hirshfeld
surface plots in Linux Xubuntu 18.04.3LTS.

-   By default CrystalExplorer will remove working files right after
    providing the visual output. To retain the `csx` files needed
    here, access the preferences menu (File -> Preferences) and
    uncheck the corresponding entry located under the expert tab, and
    close the preference menu. Then, load the `.cif` and compute the
    Hirshfeld surface at *very high* resolution (i.e., one level
    higher than by default).
    
    Starting with `example.cif`, CrystalExplorer will provide you
    `example_example.csx`.  For convenience of file management, it is
    recommended to shorten the file name to `example.csx` though.

-   Compile the Fortran sourcecode, for example with the freely
    available `gfortran`. The then obtained executable allows you to
    normalize CrystalExplorer’s output (the `.csx` file) and write the
    result into a `.dat` file. Similar to CrystalExplorer, you opt for
    either one of the following three map ranges to be considered for
    output:
    
    -   standard map: d<sub>i</sub> and d<sub>e</sub> in the range of 0.40&#x2026;2.6 &Aring;
        (221 bins in each dimension),
    
    -   translated map: d<sub>i</sub> and d<sub>e</sub> in the range of 0.8&#x2026;3.0 &Aring;
        (221 bins in each dimension), or
    
    -   extended map: d<sub>i</sub> and d<sub>e</sub> in the range of 0.4&#x2026;3.0 &Aring;
        (261 bins in each dimension)
    
    with a resolution of about 0.01 &Aring;/bin. For an initial survey,
    the extended map may be the most suitable.
    
    On the CLI, your instructions are:
    
        gfortran fingerprint.F90 -o fingerprint.x
        ./fingerprint.x in.cxs [standard | translated | extended] out.dat

-   The difference between two normalized 2D Hirshfeld maps, for
    example `map_A.dat` and `map_B.dat` may be assessed with the C
    program `diff_finger.c` . After compilation of an executable,
    e.g. with the freely available `gcc` , indicate the two files to
    compare with each other and the file to write the results to.
    
    On the CLI, your instructions are:
    
        gcc diff_finger.c -o diff_finger
        ./diff_finger map_A.dat map_B.dat > diff_map_A_map_B.dat
    
    For obvious reasons only maps of same type (standard, translated,
    extended) should be compared this way with each other.

-   Aiming for a representation quite similar to the one by
    CrystalExplorer, the normalized 2D fingerprint maps may be
    generated with `plot_finger.sh`.  The difference of two normalized
    2D fingerprint maps may be plot in a blue-white-red scale with
    `plot_diff_finger.sh`.  *Possibly* both scripts have to be
    provided the 'executable bit' prior to their deployment, which may
    be adjusted at a local level without administrator privileges.
    Both scripts are set up to generate a 4094 &times; 4094 px wide
    .png file.
    
    Hence, your instructions on the CLI follow the pattern of:
    
        chmod u+x plot_finger.sh           # provision of the executable bit
        ./plot_finger.sh example.dat       # plot of a 2D fingerprint map
        ./plot_diff_finger.sh example.dat  # plot the difference fingerprint map
