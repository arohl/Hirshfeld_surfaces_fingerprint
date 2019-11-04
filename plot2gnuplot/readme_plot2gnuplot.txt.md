
# Table of Contents



This folder, plot2gnuplot, contains a few bash scripts requiring a
.dat data file and gnuplot.  Because these relate to visualization,
rather than to the computation the Fortran, C and Ruby code provide,
they were brought into this sub-folder.  This readme provides a brief
description about these scripts relaying to gnuplot.  They either

-   provide 2D fingerprint maps of Hirshfeld surfaces as already
    introduced by CrystalExplorer.  Their expected input is the .dat
    file provided by the compiled Fortan executable of fingerprint.F90.
    These scripts are named in line of plot<sub>finger</sub>.  Or

-   provide difference maps of 2D fingerprints.  Their expected input is
    the .dat file provided by the compiled C executable diff<sub>finger.c</sub>.
    These scripts are named in line of plot<sub>diff</sub><sub>finger</sub>.

At present, the sub-folder contains these scripts:

-   plot<sub>finger</sub><sub>png.sh</sub>, added 2019-11-03.
    
    Initially provided as plot<sub>finger</sub> by Paolo Raiteri and Andrew Rohl
    to provide a fingerprint map in a color scheme as seen in
    CrystalExplorer (version 17.5).  After regrouping the instructions,
    the .png generated now equally contains both the de and di label, as
    well as the stem of the file read.  In reference to the original,
    the point size of was lowered to prevent overlap of the tiles within
    the scatter plot.  Assuming a 300 dpi resolution, the bitmap
    (3674x3229 px) is about 12.2 x 10.8 in or about 4.8 x 4.2 cm wide.

-   plot<sub>diff</sub><sub>finger</sub><sub>png.sh</sub>, added 2019-11-03.
    
    Initially set up as plot<sub>diff</sub><sub>finger</sub> by Paolo Raiteri and Andrew
    Rohl to provide a difference map of two Hirshfeld surface
    fingerprints.  The map's color scheme ranges from blue (negative
    values) over white (neuter level) to red (positive values).
    Mirroring the pattern in plot<sub>finger</sub><sub>png.sh</sub>, the original script was
    reorganized, too.  Assuming a 300 dpi resolution, the .png bitmap
    (3674x3229 px) is about 12.2 x 10.8 in or about 4.8 x 4.2 cm wide.

-   plot<sub>finger</sub><sub>pdf.sh</sub>, added 2019-11-04.
    
    Derived from plot<sub>finger</sub><sub>png.sh</sub> to provide a fingerprint map with a
    color scheme as with CrystalExplorer (version 17.5) in the vector
    format .pdf.  Canvas size originally set up to 6.0 x 6.0 cm (about
    2.4 x 2.4 in).

-   plot<sub>diff</sub><sub>finger</sub><sub>pdf.sh</sub>, added 2019-11-04.
    
    Derived from plot<sub>finger</sub><sub>pdf.sh</sub> and plot<sub>diff</sub><sub>finger</sub><sub>png.sh</sub> to
    provide an export in the resolution independent .pdf format of the
    difference map of two 2D fingerprint maps.  Canvas size originally
    set up to 6.0 x 6.0 cm (about 2.4 x 2.4 in).

-   plot<sub>finger</sub><sub>pdf</sub><sub>sparse.sh</sub>, added 2019-11-04.
    
    Derived from plot<sub>finger</sub><sub>pdf.sh</sub>.  As indicated by Ethan Merrit,
    gnuplot includes the concept of 'conditional printing', which here
    considers only tiles to contribute to the scatter plot if their
    z-value is greater than zero.  By visual inspection, the difference
    may be invisible if the threshold criterion's color representation
    (here, 0-level = white) equates the visual perception (e.g., white
    paper backgroun).  However, the size of a .pdf map generated this
    way may be less than a half of a 'classic map', with similar gain in
    overall processing, too.
    
    Resolution independent .pdf export with a canvas originally set up
    to 6.0 x 6.0 cm (about 2.4 x 2.4 in).

-   plot<sub>diff</sub><sub>finger</sub><sub>pdf</sub><sub>sparse.sh</sub>, added 2019-11-04.
    
    Derived from plot<sub>diff</sub><sub>finger</sub><sub>pdf.sh</sub> and Ethan Merrit's hint about
    'conditional plotting'.  Contrasting to the former example then
    using an unilateral criterion, now a bilateral criterion is deployed
    (the absolute value of z).
    
    Resolution independent .pdf export with a canvas originally set up
    to 6.0 x 6.0 cm (about 2.4 x 2.4 in).

-   plot<sub>finger</sub><sub>png</sub><sub>sparse.sh</sub>, added 2019-11-04.
    
    Derived from plot<sub>finger</sub><sub>png.sh</sub>, starting with an otherwise
    transparent .png 4096x4096 px bitmap and requesting the 'conditional
    printing'.  In comparison to the output by analogue output script
    plot<sub>finger</sub><sub>pdf</sub><sub>sparse.sh</sub>, the volume of the .png file actually is
    10&#x2026;20% greater than the .pdf file.
    
    Assuming a 300 dpi resolution, the .png bitmap (3674x3229 px)
    eventually provided is about 12.2 x 10.8 in (about 4.8 x 4.2 cm).

-   plot<sub>diff</sub><sub>finger</sub><sub>png</sub><sub>sparse.sh</sub>, added 2019-11-04.
    
    Derived from plot<sub>diff</sub><sub>finger</sub><sub>png.sh</sub>, modified along the patterns in
    script plot<sub>finger</sub><sub>png</sub><sub>sparse.sh</sub> (transparent canvas for pngcairo)
    as well as conditional printing with bilateral threshold (as in
    script plot<sub>diff</sub><sub>finger</sub><sub>pdf</sub><sub>sparse.sh</sub>.  Savings in file size in
    reference to a conventional .png exported map are small (about 10%),
    depending on picture viewer, however, the visual recognition of
    pixels annotated by pale red or pale blue only may be easier than
    with a bright white background.
    
    Assuming a 300 dpi resolution, the .png bitmap (3674x3229 px)
    eventually provided is about 12.2 x 10.8 in (about 4.8 x 4.2 cm).

END

