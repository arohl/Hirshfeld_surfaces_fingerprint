

# Background

Reading crystallographic models as `*.cif` file,
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
two polymorphs of benzamide (fig. [3](#org949c4d4)).  The scope of this
analysis may be extended to iso-structural materials, too.

![img](./doc_support/alignment_normal.png "2D fingerprint maps of Hirshfeld surfaces for CCDC model BZAMID01 and BZAMID11 (left and left center), derived from the analysis by CrystalExplorer at *very high* resolution (d<sub>e</sub> and d<sub>i</sub> in the range of 0.40&#x2013;3.00 &Aring;, with a 0.01 &Aring; increment in the plane).  Qualitative difference assignment by superposition as provided by ImageMagick (right center), red pixels indicate difference between the two images inspected.  Simultaneous qualitative and quantitative spatial information provided by the *difference map* generated with the scripts of this repository (right).")


# Basic use

The following sequence describes the analysis of the two benzamide
model data on Linux Xubuntu 18.04.3LTS with CrystalExplorer
(version 17.5).  The results of computation were visualized with
`gnuplot` (version 5.2.7beta).

By default CrystalExplorer removes intermediate working files,
including the `.csx` this analysis requires.  To retain this
information, access the preferences menu (File -> Preferences) and
uncheck the corresponding entry located under the expert tab. Now
close the preference menu and load the `*.cif` file in question.
Compute the Hirshfeld surface at *very high* resolution (i.e., one
level higher than by default).


## Direct work with `fingerprint.f90`, `diff_finger.c` and `sum_abs_diffs.rb`

The `*.cxs` files generated by CrystalExplorer are to be
normalized by `fingerprint.90`,<sup><a id="fnr.6" class="footref" href="#fn.6">6</a></sup> requiring a compiler such
as `gfortran`<sup><a id="fnr.7" class="footref" href="#fn.7">7</a></sup> or `gcc`<sup><a id="fnr.8" class="footref" href="#fn.8">8</a></sup> to compile an executable by

    gfortran fingerprint.f90 -o fingerprint.x

and subsequently deployed in a a pattern of

    ./fingerprint.x  input.cxs [standard | translated | extended] output.dat

with `standard`, `translated`, or `extended` as three, mutually
excluding map ranges to consider ([0.4,2.6], [0.8,3.0], or
[0.4,3.0] &Aring;) for the normalized 2D fingerprint map deposit in
file `output.dat`.

The computation of the *difference map* of 2D fingerprints
requires `diff_finger.c` to be compiled, e.g. with `gcc`<sup><a id="fnr.8.100" class="footref" href="#fn.8">8</a></sup> by

    gcc diff_finger.c -o diff_finger

and the subsequent deployment of the executable in a pattern of

    ./diff_finger input_A.dat input_B.dat > difference.dat

to compute the difference map deposit in `difference.dat`.
Requiring an installation of Ruby, these difference maps may be
condensed to a single figure of merit print to the CLI by a call
of

    ruby sum_abs_diffs.rb difference.dat

Sub-folder `plot2gnuplot` contains `bash` scripts to facilitate
the *visual inspection* of either 2D fingerprint, or difference
map with gnuplot<sup><a id="fnr.9" class="footref" href="#fn.9">9</a></sup> if called in a pattern like

    ./plot_finger_png.sh BZAMID01.dat

As indicated in the file names of these `bash` scripts, the output
format of the plots generated by `gnuplot` is either bitmap
`*.png`, or resolution independent `*.pdf`.  Possibly, they
require provision of the executable bit by (`chmod u+x
     example.sh`) to work after check-out.


## Python moderated interaction

Starting with this release, Python script `Hirshfeld_moderator.py`
is set up to facilitate the interaction with the `*.cxs` files in
Linux.  Its *help* menu is invoked by

    python3 Hirshfeld_moderator.py -h

After its deposit with `fingerprint.f90`, `diff_finger.c`, and
`sum_abs_diffs.rb` into the root of the project, the suggested
sequence to work with this wrapper is to *list* (toggle `-l`),
then to *copy* (toggle `-c`) the `*.cxs` into a dedicated
sub-folder, `cxs_workshop`.  The two operations retrieve the raw
data `*.cxs` either in the same root folder as the current
working directory, or in sub-folders to the current work
directory.  If present, the file names of `*.cxs` files *already
copied to `cxs_workshop`* are automatically truncated at first
underscore.

The *normalization* of the `*.cxs` yielding 2D fingerprint `*.dat`
files (extended map range) is invoked by the toggle `-n`.  The
script stops at this level if either the source code,
`fingerprint.f90`, is missing; or if the compilation with either
`gfortan` (default), or `gcc` (backup solution) fails.

The *computation of difference maps* is launched by toggle `-c`
with all 2D fingerprint maps identified in sub-folder
`cxs_workshop`.  Again, the moderator script will halt the
execution if either the source code, `diff_finger.c` is missing,
or if the compilation of the source code (`gcc`) fails.  The
difference maps are stored in `diff*.dat` files.

Assuming a callable installation of ruby, toggle `-r` will
determine the *ruby difference number* on all difference maps in
`cxs_workshop`.  The output on the CLI may be redirected into a
permanent record, e.g. by

    python3 Hirshfeld_moderator.py -r > report.txt

The absence of ruby will not affect other stages of the analysis.

The visual inspection of the results is relayed to gnuplot with
third-party module `Gnuplot.py`, which freely is available e.g.,
with `pip`.<sup><a id="fnr.10" class="footref" href="#fn.10">10</a></sup>  To provide an initial *overview*, call

    python3 Hirshfeld_moderator.py -o

The `*.png` in low resolution and dimension are intended to
decide if a display with the standard map range ([0.4,2.6] &Aring;,
lower left square) or translated map range ([0.8,3.0] &Aring;, upper
right square) may be more suitable than the current perspective
in extended map range ([0.4,3.0] &Aring;).

![img](./doc_support/survey.png "Example survey of 2D fingerprint maps (left, center) and difference map plot (right). Intended as guidance for setting up subsequent plots in high resolution, frames mark standard map range (left bottom, dashes) and translated map range (right atop, dots), respectively, while displaying the extended range.  The right bottom corner reports the maximal and minimal *z*-value read from the `.dat` file.")

The source code by Paolo Raiteri and Andrew Rohl suggests to
constrain the *z*-scale to [0,0.08] (2D fingerprint maps) and
[-0.025:0.025] (difference maps).  Especially for the scrutiny of
sets of Hirshfeld surface analyses, it may be particularly useful
to adjust these limits for whole series (cf. below).  It is for
this reason the lowest and highest *z*-value in the `*.dat` files
are reported both in the images, as well as in a permanent record
written, `gp_report.txt`.<sup><a id="fnr.11" class="footref" href="#fn.11">11</a></sup>

The high-resolution plots about 2D *fingerprint* or *difference
maps* are provided either as `*.pdf`, or `*.png`.  This is
concatenated in the instruction, e.g.,

    python3 Hirshfeld_moderator --dpng e

to generate *difference maps* as `*.png`.  The explicit `e` sets
the plot for the *extended* map range ([0.4,3.0] &Aring;).
Alternatively, `s` calls for the *standard*, and `t` for the
*translated* map range of [0.4,2.6] &Aring;, and [0.8,3.0] &Aring;.

Both types of high-resolution maps deploy *as adjustable
default* a *z*-range of [0.00,0.08] (2D fingerprint maps) and
[-0.025,0.025] (difference maps).  It is optional to override
these defaults in line of, e.g.

    python3 Hirshfeld_moderator.py --dpng e --zmax 0.03

to limit the *z*-range of the difference map to [-0.03,0.03].

To ease comparison across series of data, the highest and lowest
*z*-value recorded in the `*.dat` data will be displayed in the
plot.  Equally, `Hirshfeld_moderator.py` may exchange the color
palettes used by the optional toggle `-a`.  This then activates
palettes perceptually safer, e.g. for an output constrained to
gray-scale.

Independent of the color-palette selected, the high-resolution
output may use a neuter gray background.  This is invoked by the
optional toggle `-g`.

Below, the effect of color palette and background selection is
illustrated.  They each display the fingerprint about either CSD
model `BZAMID01`, or `BZAMID11`; the difference plot for the two
fingerprints as determined by ImageMagick's `compare`, and the
computed difference map as displayed by gnuplot.

![img](./doc_support/alignment_normal.png "Gnuplot's output of 2D fingerprint maps (very left, left center), ImageMagick's difference with `compare` (right center), and gnuplot's difference map in default mode.")

![img](./doc_support/alignment_normal_gray.png "Processing the data with the optional optional neutral gray background (toggle `-g`), default color palettes.")

![img](./doc_support/alignment_alternate.png "Optional processing with the alternate, perceptual safer color palettes `cubehelix` in 2D fingerprints, and Kenneth Moreland's improved diverging palette `bent-cool-warm` (64 levels); toggle `-a`.")

![img](./doc_support/alignment_alternate_gray.png "Optional simultaneous processing with the alternate color palettes (toggle `-a`) and the optional neutral gray background (toggle `-g`).")

Note that the diagrams plot may be either `*.png` (e.g., by invoking
`--dpng e`), or resolution independent format `*.pdf` (e.g., by
invoking `--dpdf s`).  Thanks to gnuplot's *conditional
plotting*,<sup><a id="fnr.12" class="footref" href="#fn.12">12</a></sup> the vector-based output tends to be less large than
the bitmap.


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

<sup><a id="fn.7" href="#fnr.7">7</a></sup> In preparation of this guide, `gfortran` in version 7.4.0 was
used successfully.

<sup><a id="fn.8" href="#fnr.8">8</a></sup> In preparation of this guide, `gcc` in version 7.4.0 was used
successfully.

<sup><a id="fn.9" href="#fnr.9">9</a></sup> <http://gnuplot.info>

<sup><a id="fn.10" href="#fnr.10">10</a></sup> In preparation of this guide, `Gnuplot.py` in version 1.8 was
used successfully.

<sup><a id="fn.11" href="#fnr.11">11</a></sup> The entries may be sorted, e.g., by `sort -k4 -n input.txt -o
output.txt`, as  `sort` is part of the GNU coreutils.

<sup><a id="fn.12" href="#fnr.12">12</a></sup> Thanks to Ethan Merrit who suggested this additional
improvement in a private communication.  The reduction of file volume
is especially observed for the generation of `*.pdf`.
