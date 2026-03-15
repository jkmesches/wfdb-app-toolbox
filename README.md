## About this fork

Patched version of the WFDB App Toolbox (0.10.0). The upstream toolbox
has not been updated since 2014 and is broken on modern MATLAB due to
JVM incompatibilities, PhysioNet's HTTPS migration, and several parsing
bugs. This fork fixes those issues and rebuilds the native libraries
with SSL support for all platforms.

See [upstream issue #188](https://github.com/ikarosilva/wfdb-app-toolbox/issues/188)
for the original bug report.

### What's fixed

The main issues were that `rdsamp` crashed on any JVM newer than 7,
`setenv('WFDB', ...)` was silently ignored for local datasets, and the
bundled `libcurl` couldn't follow HTTPS redirects. On top of that,
`wfdbdownload` would cache HTTP error pages as valid signal data,
`wfdbdesc` crashed on any record with colons in the timestamp,
`ecgpuwave` interpreted sample numbers as seconds, and `msentropy`
crashed parsing its own output.

All of these are fixed. The native libraries have been rebuilt with
SSL-capable `libcurl` on Linux, macOS (ARM64), and Windows. The Java
jar is compiled for JVM 8+.

Licensed under the same [GPL v3](LICENSE) as the original.

## Introduction
The WFDB Toolbox for MATLAB and Octave is a set of Java, GUI, and m-code wrapper functions,
which make system calls to [WFDB Software Package](http://physionet.org/physiotools/wfdb.shtml) and other PhysioToolkit applications. The website for the toolbox (which includes the installation instructions) can be found at:

http://physionet.org/physiotools/matlab/wfdb-app-matlab/

Using the WFDB Toolbox, MATLAB and Octave users have access to over 50 [PhysioNet](http://physionet.org/) databases (over 4 TB of physiologic signals including ECG, EEG, EMG, fetal ECG, PLETH, PPG, ABP, respiration, and more).
Additionally, most of these databases are also accompanied by metadata such as expert annotations of
physiologically relevant events in WFDB annotation files. These can include, for example, 
cardiologists' beat and rhythm annotations of ECGs, or sleep experts' hypnograms (sleep stage annotations) 
of polysomnograms. All of these physiologic signals and annotations can be read on demand from the
PhysioNet web server and its mirrors using the toolbox functions, or from local copies if you choose 
to download them. This feature allows your code to analyze the wide range of physiologic signals 
available from PhysioBank without the need to download entire records and to store them locally.
The Toolbox is open-source (distributed under the GPL). The toolbox includes a GUI (WFDBRECORDVIEWER)
for facilitating the browsing, exploration, and analysis of WFBD records stored locally on the users machine, 
or remotely in PhysioNet's [databases](http://physionet.org/physiobank/database/DBS).

## Available Databases

For a list of available databases accessible through the WFDB Toolbox, see:

http://physionet.org/physiobank/database/DBS

## Installing this fork

Download the latest release zip from the
[Releases](https://github.com/jkmesches/wfdb-app-toolbox/releases) page,
extract it, then in MATLAB:

```matlab
% Remove any existing WFDB toolbox from the path
[old_path]=which('rdsamp');if(~isempty(old_path)) rmpath(old_path(1:end-8)); end

% Add the patched toolbox
addpath('/path/to/extracted/mcode'); savepath;

% Test it
[sig, Fs] = rdsamp('mitdb/100', [], 1000);
plot(sig(:,1));
```

To use locally downloaded datasets (e.g. PTB-XL):

```matlab
setenv('WFDB', '/path/to/ptb-xl-dataset .');
[sig, Fs] = rdsamp('records500/00000/00001_hr');
```

### Installing the original (unpatched) from PhysioNet

The upstream version is available at:
https://physionet.org/physiotools/matlab/wfdb-app-matlab/

Note: the upstream version has known issues with MATLAB R2020+ and newer
PhysioNet HTTPS endpoints. See issue
[#188](https://github.com/ikarosilva/wfdb-app-toolbox/issues/188).

## Known issues

These are inherited from upstream and not fully fixed here:

- `bxb` / `mxm` — native binaries silently produce no output on some
  records (especially `mat2wfdb`-generated ones). This fork adds a clear
  error instead of crashing.
- `edr` — one struct indexing bug is fixed, but a second indexing error
  in the gain/QRS boundary code remains.
- `corrint` — the native binary was never shipped upstream. The MATLAB
  wrapper exists but has nothing to call.

Tested on MATLAB R2025b (Linux x86_64): 42/52 pass, 3 fail (above),
7 skip (GUIs and databases needing specific access).

## Building from source

Building the toolbox requires:
- The GNU C compiler (GCC)
- The GNU Fortran compiler (gfortran)
- GNU Make
- GNU Autoconf
- GNU Libtool
- Java Development Kit
- Ant

To build the toolbox, simply run 'make' in the top-level directory.
This will automatically download various dependencies from PhysioNet
and elsewhere (see 'mcode/nativelibs/Makefile' for details.)

## Reference & Toolbox Technical Overview

[An Open-source Toolbox for Analysing and Processing PhysioNet Databases in MATLAB and Octave.
I Silva, GB Moody, Journal of Open Research Software 2 (1), e27](http://openresearchsoftware.metajnl.com/article/view/jors.bi/77)


[WFDB Software Package](http://physionet.org/physiotools/wfdb.shtml) 

