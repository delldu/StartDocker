FROM ubuntu:20.04
MAINTAINER Dell Du <18588220928@163.com>

ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /build_workspace

RUN apt-get -y update
RUN apt install -y \
	gcc g++ \
	make autoconf libtool cmake automake ninja-build \
	git \
	python3-setuptools python3-pip && \
	apt clean

# Install meson
RUN python3 -m pip install meson==0.54.0 && cp /usr/local/bin/meson /usr/bin/

# Refrence 9, Hard dependencies 
RUN apt install -y libpng-dev libjpeg-dev libtiff-dev liblcms2-dev && \
	apt clean

#   1. You need to have installed a recent version of pkg-config (>= 3.0) available
#      from https://www.freedesktop.org/software/pkgconfig/.
RUN apt install -y pkg-config && \
	apt clean

#   2. You need intltool (at least 0.40.1, but preferably a newer version).
#      Intltool can be downloaded from
#      https://ftp.gnome.org/pub/gnome/sources/intltool/
#      You also need gettext version 0.19 or over. Earlier gettext had
#      issues with script-fu localization, ending up in incomplete GIMP localization.
RUN apt install -y intltool gettext && \
	apt clean

#   3. You need to have GEGL version 0.4.28 or newer and babl version
#      0.1.74 or newer. You can get them from https://gegl.org/ or clone
#      them from the GNOME git repository:

#        https://gitlab.gnome.org/GNOME/babl.git
#        https://gitlab.gnome.org/GNOME/gegl.git

RUN git clone https://gitlab.gnome.org/GNOME/babl.git
# Build babl
RUN cd babl && 
	mkdir build && \
	meson --prefix=/usr .. && \
	ninja && ninja install && cd .. && \
	rm -rf build && \
	cd ..

RUN git clone https://gitlab.gnome.org/GNOME/gegl.git
# Build dependencies
RUN apt install -y libglib2.0-dev libjson-glib-dev && \
	apt clean

# Build gegl
RUN cd gegl && \
	mkdir build && \
	meson --prefix=/usr .. && \
	ninja && ninja install && cd .. && \
	rm -rf build && \
	cd ..

#   4. You need to have installed GTK+ version 3.22.29 or newer.
#      GIMP also needs a recent version of GLib (>= 2.56.2), GDK-Pixbuf
#      (>= 2.30.8), and Pango (>= 1.44.0). Sources for these can be grabbed
#      from ftp://ftp.gtk.org/.

RUN apt install -y libgtk-3-0 libgdk-pixbuf2.0-common libpango-1.0-0 && \
	apt clean

#   5. We use cairo >= 1.14.0, which is hosted at
#      https://www.cairographics.org/.
RUN apt install -y libcairo2 && \
	apt clean

#   6. We require PangoCairo, a Pango backend using Cairo. Make sure you
#      have Cairo, FreeType2 and fontconfig installed before you compile
#      Pango. GIMP depends on freetype2 being newer than version 2.1.7
#      and fontconfig 2.12.4 or newer. Older versions are known to have
#      bugs that seriously affect the stability of GIMP.

#      We also require HarfBuzz 1.0.5 or newer, an
#      OpenType text shaping tool. As this is a dependency for Pango, you
#      will likely have it installed, but you may have to install a
#      development package for the headers.

RUN apt install -y libpangocairo-1.0-0 libfreetype-dev fontconfig libharfbuzz-dev && \
	apt clean

#   7. The file-compressor plug-in requires zlib, libbzip2, and liblzma to
#      be installed. All these libraries are required dependencies.
RUN apt install -y zlib1g-dev libbz2-dev liblzma-dev && \
	apt clean

#   8. For metadata access GIMP requires the gexiv2 0.10.6 or newer library.
#      It is hosted at: https://wiki.gnome.org/Projects/gexiv2
RUN apt install -y libgexiv2-dev && \
	apt clean

#   9. libpng, libjpeg, libtiff and lcms are hard dependencies that can
#      not be disabled.
# RUN apt install -y libpng-dev libjpeg-dev libtiff-dev liblcms2-dev

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

# Build dependencies
RUN apt install -y libjson-c-dev libtool && \
	apt clean
RUN git clone https://github.com/mypaint/libmypaint
# Build libmypaint
RUN cd libmypaint && \
	git checkout v1.6.0 && ./autogen.sh && ./configure && make && make install && \
	cd ..

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
# Build mypaint-brushes
RUN cd mypaint-brushes && \
	git checkout v1.3.1 && \
	./autogen.sh && ./configure && make && make install && \
	cd ..

#  12. You may want to install other third party libraries or programs
#      that are needed for some of the available plug-ins. We recommend
#      to check that the following libraries are installed: openjpeg,
#      webkit, libmng, librsvg, libwmf, libaa and libgs (Ghostscript).
RUN apt install -y libopenjp2-7-dev libwebkit2gtk-4.0-dev libmng-dev librsvg2-dev \
	libwmf-dev libaa1-dev libgs-dev && \
	apt clean

#  13. HEIF support depends on the libheif library. If you don't have
#      access to pre-built packages, the code is available at:
#         https://github.com/strukturag/libheif

#      Make sure you build libheif with libde265 and libx265 support (for
#      respectively decoding and encoding of HEVC, i.e. HEIC files), and
#      libaom decoder and encoder (for AV1, i.e. AVIF files), otherwise
#      the plug-in is mostly useless.
RUN apt install -y libheif-dev && \
	apt clean

#  14. GObject Introspection requires the following dependencies to be
#      built and installed with introspection as well: babl, cairo,
#      GdkPixbuf, GEGL, GIO, GLib, GObject and GTK+.
RUN apt install -y gobject-introspection && \
	apt clean

#  19. Summary of required packages and what version you need:
RUN apt install -y libappstream-glib-dev libgtk-3-dev libgtk-3-bin libpoppler-glib-dev poppler-data && \
	apt clean

#  20. Summary of optional packages:
RUN apt install -y libgirepository1.0-dev valac && \
	apt clean

RUN apt autoremove



#  iso-codes libxpm-dev
