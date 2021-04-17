FROM ubuntu:20.04
MAINTAINER Dell Du <18588220928@163.com>

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get -y update
RUN apt install -y gcc g++ make autoconf cmake git python3-setuptools python3-pip

RUN python3 -m pip install meson==0.54.0 && cp /usr/local/bin/meson /usr/bin/

RUN apt install ninja-build=1.10.0-1build1

#   1. You need to have installed a recent version of pkg-config (>= 3.0) available
#      from https://www.freedesktop.org/software/pkgconfig/.
RUN apt install -y pkg-config=0.29.1-0ubuntu4

#   2. You need intltool (at least 0.40.1, but preferably a newer version).
#      Intltool can be downloaded from
#      https://ftp.gnome.org/pub/gnome/sources/intltool/
#      You also need gettext version 0.19 or over. Earlier gettext had
#      issues with script-fu localization, ending up in incomplete GIMP localization.
RUN apt install -y intltool=0.51.0-5ubuntu1
RUN apt install -y gettext=0.19.8.1-10build1

#   3. You need to have GEGL version 0.4.28 or newer and babl version
#      0.1.74 or newer. You can get them from https://gegl.org/ or clone
#      them from the GNOME git repository:

#        https://gitlab.gnome.org/GNOME/babl.git
#        https://gitlab.gnome.org/GNOME/gegl.git
# Refrence 9
RUN apt install -y libpng-dev libjpeg-dev libtiff-dev liblcms2-2=2.9-4 liblcms2-dev=2.9-4 liblcms2-utils=2.9-4
RUN apt install -y libglib2.0-dev libjson-glib-dev

RUN git clone https://gitlab.gnome.org/GNOME/babl.git
# build babl
RUN cd babl && meson build && \
	cd build && ninja && ninja install && cd .. && \
	cd ..
# RUN apt install -y libglib2.0-0
RUN git clone https://gitlab.gnome.org/GNOME/gegl.git
# libjson-glib-1.0-common libjson-glib-1.0-0
# Build gegl
RUN cd gegl && meson _build && ninja -C _build && ninja -C _build install && cd ..

#   4. You need to have installed GTK+ version 3.22.29 or newer.
#      GIMP also needs a recent version of GLib (>= 2.56.2), GDK-Pixbuf
#      (>= 2.30.8), and Pango (>= 1.44.0). Sources for these can be grabbed
#      from ftp://ftp.gtk.org/.

RUN apt install -y libgtk-3-0=3.24.18-1ubuntu1
# RUN apt install -y libglib2.0-0=2.64.2-1~fakesync1
RUN apt --fix-broken install
RUN apt install -y libgdk-pixbuf2.0-common libgdk-pixbuf2.0-0
#=2.40.0+dfsg-3
RUN apt install -y libpango-1.0-0=1.44.7-2ubuntu4

#   5. We use cairo >= 1.14.0, which is hosted at
#      https://www.cairographics.org/.
RUN apt install -y libcairo2=1.16.0-4ubuntu1

#   6. We require PangoCairo, a Pango backend using Cairo. Make sure you
#      have Cairo, FreeType2 and fontconfig installed before you compile
#      Pango. GIMP depends on freetype2 being newer than version 2.1.7
#      and fontconfig 2.12.4 or newer. Older versions are known to have
#      bugs that seriously affect the stability of GIMP.

#      We also require HarfBuzz 1.0.5 or newer, an
#      OpenType text shaping tool. As this is a dependency for Pango, you
#      will likely have it installed, but you may have to install a
#      development package for the headers.

RUN apt install -y libpangocairo-1.0-0=1.44.7-2ubuntu4
RUN apt --fix-broken install
RUN apt install -y libfreetype6 libfreetype-dev
#=2.10.1-2
RUN apt install -y fontconfig=2.13.1-2ubuntu3

# RUN apt install -y libpangoft2-1.0-0
RUN apt install -y libharfbuzz-dev=2.6.4-1ubuntu4

#   7. The file-compressor plug-in requires zlib, libbzip2, and liblzma to
#      be installed. All these libraries are required dependencies.
RUN apt --fix-broken install
RUN apt install -y zlib1g
#=1:1.2.11.dfsg-2ubuntu1
RUN apt install -y bzip2=1.0.8-2
RUN apt install -y liblzma-dev
#=5.2.4-1

#   8. For metadata access GIMP requires the gexiv2 0.10.6 or newer library.
#      It is hosted at: https://wiki.gnome.org/Projects/gexiv2
RUN apt install -y libgexiv2-2=0.12.0-2

#   9. libpng, libjpeg, libtiff and lcms are hard dependencies that can
#      not be disabled.

#  10. For MyPaint brushes, brushlib (libmypaint) 1.3.0 is used.
#      The libmypaint repository is hosted at:
#         https://github.com/mypaint/libmypaint
#      If installing from repository, do not install the master branch!
#      Checkout the last tag "v1.y.z" from `libmypaint-v1` branch instead
#      (for instance "v1.6.1" tag at time of writing), or simply install
#      from a tarball or from your favorite package manager.
#      In particular, do NOT install tags or release tarballs versionned
#      "v2.y.z". GIMP depends on the version 1 of libmypaint and is
#      incompatible with the version 2 (which is still experimental anyway
#      and has no stable release at time of writing).
RUN apt install -y libmypaint-1.5-1=1.5.1-1


#  11. We also need the mypaint-brushes data package:
#         https://github.com/mypaint/mypaint-brushes

#      If installing from repository, install from branch "v1.3.x" or the
#      last tag "v1.y.z" (e.g. "v1.3.1" at time of writing).
#      In particular do NOT install from `master` branch which installs
#      brushes incompatible with GIMP (the `master` branch brushes are
#      targetted to libmypaint upcoming v2, not v1).

#      Also this is a data packages and therefore it will install the
#      pkg-config file inside `$PREFIX/share/pkgconfig/`. If you install
#      mypaint-brushes from repository in a non-standard prefix, you will
#      have to make sure your $PKG_CONFIG_PATH environment variable also
#      lists `$PREFIX/share/pkgconfig/`.
RUN git clone https://github.com/mypaint/mypaint-brushes
RUN cd mypaint-brushes && \
	git checkout v1.3.1 && \
	./autogen.sh && ./configure && make && make install && cd ..


#  12. You may want to install other third party libraries or programs
#      that are needed for some of the available plug-ins. We recommend
#      to check that the following libraries are installed: openjpeg,
#      webkit, libmng, librsvg, libwmf, libaa and libgs (Ghostscript).

#  13. HEIF support depends on the libheif library. If you don't have
#      access to pre-built packages, the code is available at:
#         https://github.com/strukturag/libheif

#      Make sure you build libheif with libde265 and libx265 support (for
#      respectively decoding and encoding of HEVC, i.e. HEIC files), and
#      libaom decoder and encoder (for AV1, i.e. AVIF files), otherwise
#      the plug-in is mostly useless.

#  14. GObject Introspection requires the following dependencies to be
#      built and installed with introspection as well: babl, cairo,
#      GdkPixbuf, GEGL, GIO, GLib, GObject and GTK+.

#  19. Summary of required packages and what version you need:

RUN apt install -y libappstream-glib-dev=0.7.16-1ubuntu1

RUN apt install -y librsvg2-2
# =2.48.2-1
RUN apt install -y libpoppler-glib-dev=0.86.1-0ubuntu1
RUN apt install -y poppler-data=0.4.9-2


#  20. Summary of optional packages:
RUN apt install -y libopenjp2-7-dev
#=2.3.1-1ubuntu4