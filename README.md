# About

A collection of tools Standard Ebooks uses to produce its ebooks, including basic setup of ebooks, text processing, and build tools.

Installing this toolset using `pip` makes the `se` command line executable available. Its various commands are described below, or you can use `se help` to list them.

# Installation

The toolset requires Python >= 3.5.

## Ubuntu 18.04 (Bionic) users

```shell
# Install some pre-flight dependencies.
sudo apt install -y python3-pip libxml2-utils librsvg2-bin libimage-exiftool-perl imagemagick jarwrapper default-jre inkscape calibre curl git

# Install required fonts.
mkdir -p ~/.local/share/fonts/
curl -s -o ~/.local/share/fonts/LeagueSpartan-Bold.otf "https://raw.githubusercontent.com/theleagueof/league-spartan/master/LeagueSpartan-Bold.otf"
curl -s -o ~/.local/share/fonts/OFLGoudyStM.otf "https://raw.githubusercontent.com/theleagueof/sorts-mill-goudy/master/OFLGoudyStM.otf"
curl -s -o ~/.local/share/fonts/OFLGoudyStM-Italic.otf "https://raw.githubusercontent.com/theleagueof/sorts-mill-goudy/master/OFLGoudyStM-Italic.otf"

# Refresh the local font cache.
sudo fc-cache -f

# Download and install the required version of epubcheck.
wget --quiet -O /tmp/epubcheck.zip $(wget --quiet -O - https://api.github.com/repos/w3c/epubcheck/releases/latest | grep -E --only-matching "\"browser_download_url\":\s*\"(.*?)\"" | sed "s/\"browser_download_url\":\s*//g" | sed "s/\"//g")
unzip -d $HOME/.local/share/ /tmp/epubcheck.zip
mv $HOME/.local/share/epubcheck-* $HOME/.local/share/epubcheck
sudo chmod +x $HOME/.local/share/epubcheck/epubcheck.jar
sudo ln -s $HOME/.local/share/epubcheck/epubcheck.jar /usr/local/bin/epubcheck

# Install the toolset.
pip3 install standardebooks

# Either link the `se` executable to a place that's in your path...
sudo ln -s $HOME/.local/bin/se /usr/local/bin/se

# ...or, add pip's binary location (~/.local/bin) to your $PATH to be able to access the `se` command.
# To make this permanent, add ~/.local/bin to your $PATH in ~/.bashrc or ~/.zshrc
export PATH=$PATH:~/.local/bin

# Optional: ZSH users can install tab completion.
sudo ln -s $HOME/.local/lib/python3.*/site-packages/se/completions/zsh/_se /usr/share/zsh/vendor-completions/_se && hash -rf && compinit

# Optional: Bash users can install tab completion.
sudo ln -s $HOME/.local/lib/python3.*/site-packages/se/completions/bash/se /usr/share/bash-completions/completions/se
```

## Fedora users

```shell
# Install some pre-flight dependencies.
sudo dnf install firefox ImageMagick calibre librsvg2-tools vim inkscape libxml2 perl-Image-ExifTool java-1.8.0-openjdk

# Install required fonts.
mkdir -p ~/.local/share/fonts/
curl -s -o ~/.local/share/fonts/LeagueSpartan-Bold.otf "https://raw.githubusercontent.com/theleagueof/league-spartan/master/LeagueSpartan-Bold.otf"
curl -s -o ~/.local/share/fonts/OFLGoudyStM.otf "https://raw.githubusercontent.com/theleagueof/sorts-mill-goudy/master/OFLGoudyStM.otf"
curl -s -o ~/.local/share/fonts/OFLGoudyStM-Italic.otf "https://raw.githubusercontent.com/theleagueof/sorts-mill-goudy/master/OFLGoudyStM-Italic.otf"

# Refresh the local font cache.
sudo fc-cache -f

# Download and install the required version of epubcheck.
wget --quiet -O /tmp/epubcheck.zip $(wget --quiet -O - https://api.github.com/repos/w3c/epubcheck/releases/latest | grep -E --only-matching "\"browser_download_url\":\s*\"(.*?)\"" | sed "s/\"browser_download_url\":\s*//g" | sed "s/\"//g")
unzip -d $HOME/.local/share/ /tmp/epubcheck.zip
mv $HOME/.local/share/epubcheck-* $HOME/.local/share/epubcheck
sudo chmod +x $HOME/.local/share/epubcheck/epubcheck.jar

# Set up a wrapper script to call the epubcheck jar file.
# Ubuntu uses `jarwrapper` to let you execute a jar file directly on the command line. Fedora does not have this capability without extra configuration.
echo -e "#\!/usr/bin/env bash\n/usr/bin/java -jar \"\$HOME/.local/share/epubcheck/epubcheck.jar\" \"\$@\"" | sudo tee /usr/local/bin/epubcheck
sudo chmod +x /usr/local/bin/epubcheck
```

## macOS users

These instructions were tested on macOS 10.12 and 10.13. Your mileage may vary. Corrections and fixes to these steps are welcomed, as the SE maintainers don’t have access to Macs.

1. Install the [Homebrew package manager](https://brew.sh). Or, if you already have it installed, make sure it’s up to date:

	```shell
	brew update
	```

2. Install dependencies:

	```shell
	# Install some pre-flight dependencies.
	brew install python epubcheck imagemagick libmagic librsvg exiftool git
	pip3 install pyopenssl

	# Install required applications.
	brew cask install java calibre xquartz inkscape

	# Install required fonts.
	curl -s -o ~/Library/Fonts/LeagueSpartan-Bold.otf "https://raw.githubusercontent.com/theleagueof/league-spartan/master/LeagueSpartan-Bold.otf"
	curl -s -o ~/Library/Fonts/OFLGoudyStM.otf "https://raw.githubusercontent.com/theleagueof/sorts-mill-goudy/master/OFLGoudyStM.otf"
	curl -s -o ~/Library/Fonts/OFLGoudyStM-Italic.otf "https://raw.githubusercontent.com/theleagueof/sorts-mill-goudy/master/OFLGoudyStM-Italic.otf"

	# Install the toolset.
	pip3 install standardebooks
	```

## Installing from Git

To install the latest version from Git instead of the version from Pip, follow the instructions above to install dependencies, but instead of running `pip3 install` run the following:

```shell
cd /PATH/TO/TOOLS/REPO
python3 setup.py bdist_wheel
pip3 install dist/*.whl
```

# Help wanted

We need volunteers to take the lead on the following goals:

- Writing installation instructions for Bash and ZSH completions for MacOS.

- Possibly adding virtualenv to the installation instructions.

# Tool descriptions

-	### `se british2american`

	Try to convert British quote style to American quote style in DIRECTORY/src/epub/text/.

	Quotes must already be typogrified using the `se typogrify` tool.

	This script isn’t perfect; proofreading is required, especially near closing quotes near to em-dashes.

-	### `se build`

	Build an ebook from a Standard Ebook source directory.

-	### `se build-images`

	Build ebook cover and titlepage images in a Standard Ebook source directory and place the output in DIRECTORY/src/epub/images/.

-	### `se clean`

	Prettify and canonicalize individual XHTML or SVG files, or all XHTML and SVG files in a source directory.  Note that this only prettifies the source code; it doesn’t perform typography changes.

-	### `se compare-versions`

	Use Firefox to render and compare XHTML files in an ebook repository. Run on a dirty repository to visually compare the repository’s dirty state with its clean state.

-	### `se create-draft`

	Create a skeleton of a new Standard Ebook.

-	### `se dec2roman`

	Convert a decimal number to a Roman numeral.

-	### `se extract-ebook`

	Extract an EPUB, MOBI, or AZW3 ebook into ./FILENAME.extracted/ or a target directory.

-	### `se find-mismatched-diacritics`

	Find words with mismatched diacritics in Standard Ebook source directories.  For example, `cafe` in one file and `café` in another.

-	### `se help`

	List available SE commands.

-	### `se hyphenate`

	Insert soft hyphens at syllable breaks in an XHTML file.

-	### `se interactive-sr`

	Use Vim to perform an interactive search and replace on a list of files. Use y/n/a to confirm (y) or reject (n) a replacement, or to replace (a)ll.

-	### `se lint`

	Check for various Standard Ebooks style errors.

-	### `se make-url-safe`

	Make a string URL-safe.

-	### `se modernize-spelling`

	Modernize spelling of some archaic words, and replace words that may be archaically compounded with a dash to a more modern spelling.  For example, replace `ash-tray` with `ashtray`.

-	### `se prepare-release`

	Calculate work word count, insert release date if not yet set, and update modified date and revision number.

-	### `se print-manifest-and-spine`

	Print `<manifest>` and `<spine>` tags to standard output for the given Standard Ebooks source directory, for use in that directory’s content.opf.

-	### `se print-toc`

	Build a table of contents for an SE source directory and print to stdout.

-	### `se recompose-epub`

	Recompose a Standard Ebooks source directory into a single HTML5 file, and print to standard output.

-	### `se reorder-endnotes`

	Increment the specified endnote and all following endnotes by 1.

-	### `se roman2dec`

	Convert a Roman numeral to a decimal number.

-	### `se semanticate`

	Apply some scriptable semantics rules from the Standard Ebooks semantics manual to a Standard Ebook source directory.

-	### `se split-file`

	Split an XHTML file into many files at all instances of `<!--se:split-->`, and include a header template for each file.

-	### `se titlecase`

	Convert a string to titlecase.

-	### `se typogrify`

	Apply some scriptable typography rules from the Standard Ebooks typography manual to a Standard Ebook source directory.

-	### `se unicode-names`

	Display Unicode code points, descriptions, and links to more details for each character in a string.  Useful for differentiating between different flavors of spaces, dashes, and invisible characters like word joiners.

-	### `se version`

	Print the version number and exit.

-	### `se word-count`

	Count the number of words in an HTML file and optionally categorize by length.


# What a Standard Ebooks source directory looks like

Many of these tools act on Standard Ebooks source directories.  Such directories have a consistent minimal structure:

	.
	|-images/
	|--cover.jpg
	|--cover.source.jpg
	|--cover.svg
	|--titlepage.svg
	|-src/
	|--epub/
	|---css/
	|----core.css
	|----local.css
	|---images/
	|----cover.svg
	|----titlepage.svg
	|---text/
	|----colophon.xhtml
	|----imprint.xhtml
	|----titlepage.xhtml
	|----uncopyright.xhtml
	|---content.opf
	|---onix.xml
	|---toc.xhtml
	|--META-INF/
	|---container.xml
	|--mimetype
	|-LICENSE.md

`./images/` contains source images for the cover and titlepages, as well as ebook-specific source images.  Source images should be in their maximum available resolution, then compressed and placed in `./src/epub/images/` for distribution.

`./src/epub/` contains the actual epub files.
