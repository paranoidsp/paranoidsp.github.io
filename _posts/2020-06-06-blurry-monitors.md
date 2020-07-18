---
layout: post
title: Blurry fonts on external monitors in MacOS Catalina
tags: [ devsetup, macos, catalina, monitor, learning, notes ]
---
* * *
The problem, the fix, and the usual rambling

### The Problem

I recently bought a new monitor, a Dell P2419H. A large screen works wonders for
my productivity, and three months of working on my 13inch laptop gave me the
push I needed to finally make this purchase.

I looked at several different screens while buying this, but the great
experience I had with quality on Dell monitors, and the matte screen drove me to
purchase this instead of a few Asus/Samsung models I was considering.

The monitor arrived, in perfect condition, but I noticed that fonts on the
monitor were blurry. This is despite turning on subpixel rendering and font
smoothing on MacOS Catalina.

* Subpixel Rendering
  ```
  defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO
  ```
  Had to eventually set this too, to turn everything extra off
  ```
  defaults -currentHost delete -globalDomain AppleFontSmoothing
  ```

* Font smoothing

  Turn it on in General Preferences.


### Why it happened, and how to fix it

The blurry fonts drove me to investigate the problem, and I found a [bunch](https://www.reddit.com/r/MacOS/comments/dkowz1/instructions_for_forcing_rgb_mode_in_catalina/) [of](https://www.mathewinkson.com/2013/03/force-rgb-mode-in-mac-os-x-to-fix-the-picture-quality-of-an-external-monitor) [people](https://spin.atomicobject.com/2018/08/24/macbook-pro-external-monitor-display-problem/)
online that seem to have faced this, and provided a neat fix.

As you can read in the links above, this is due to the Dell monitor advertising
support for [YCrCb](https://en.wikipedia.org/wiki/YCbCr), and MacOS choosing
to use that instead of RGB, which the monitor supports better. Most people
suggest using [this](https://gist.githubusercontent.com/adaugherity/7435890/raw/00ff3ead17ae77d2f1c376e90831c037b7dea7ed/patch-edid.rb) (backed up [here](https://gist.github.com/paranoidsp/b7cfd9a49c91af70181c0ba7ea95ce58) )
ruby patch script to create a new EDID override file, and
then replacing that in your System files by booting into Recovery mode.

If you have your firmware locked, you might be unable to boot into Recovery
mode. This is a problem, unless you've turned off SIP in the past, in which case
you can mount the System partition as read-write with the following commands:

```bash
sudo mount -uw /

killall finder
```

`-u` indicates changing the status of an already mounted file system, and `-w`
indicates read-write mounting.

Killing Finder is in order to force it to relaunch and recognize the file system
changes.

Once you do this, you can straight away copy the file the patch script creates
to the path in `/System/Library/Displays/Contents/Resources/Overrides/<Vendor Folder>/`

### What does this patch actually do?

This section is for people that aren't really familiar with programming. 

The layman expects things to be validated by experts, but rarely has the chance
to check for that validation. So it falls on us to encourage a culture that has
professionals vouching for ad-hoc scripts, so that eventually it is easy for
people to check if a particular solution is trustable. This implicit trust is
something that we also see in the open source ecosystem, more on this in a
separate post. 

For those of you unsure of running an arbitrary script, and not familiar enough
with programming to read the script, here's an explanation, and validation of
what it does.


This first section just fetches display data and stores it.
```ruby
require 'base64'

data=`ioreg -l -d0 -w 0 -r -c AppleDisplay`

edids=data.scan(/IODisplayEDID.*?<([a-z0-9]+)>/i).flatten
vendorids=data.scan(/DisplayVendorID.*?([0-9]+)/i).flatten
productids=data.scan(/DisplayProductID.*?([0-9]+)/i).flatten

displays = []
edids.each_with_index do |edid, i|
    disp = { "edid_hex"=>edid, "vendorid"=>vendorids[i].to_i, "productid"=>productids[i].to_i }
    displays.push(disp)
end
```

The loop after looks through all your connected displays and fetches the model info
from the EDID descriptor.

```ruby
# Process all displays
if displays.length > 1
    puts "Found %d displays!  You should only install the override file for the one which" % displays.length
    puts "is giving you problems.","\n"
elsif displays.length == 0
    puts "No display data found!  Are any external displays connected?"
end
displays.each do |disp|
    # Retrieve monitor model from EDID display descriptor
    i = disp["edid_hex"].index('000000fc00')
    if i.nil?
        monitor_name = "Display"
    else
        # The monitor name is stored in (up to) 13 bytes of text following 00 00 00 fc 00.
        # If the name is shorter than 13 bytes, it is terminated with a newline (0a) and then padded with spaces.
        monitor_name = [disp["edid_hex"][i + 10, 26].to_s].pack("H*")
        monitor_name.rstrip!            # remove trailing newline/spaces
    end

    puts "Found display '#{monitor_name}': vendor ID=#{disp["vendorid"]} (0x%x), product ID=#{disp["productid"]} (0x%x)" %
            [disp["vendorid"], disp["productid"]]
    puts "Raw EDID data:\n#{disp["edid_hex"]}"

```

Still within the loop, the EDID descriptor is modified to only show support for
RGB, and a new file is created with the new EDID values.

```ruby
    bytes=disp["edid_hex"].scan(/../).map{|x|Integer("0x#{x}")}.flatten

    puts "Setting color support to RGB 4:4:4 only"
    bytes[24] &= ~(0b11000)

    puts "Number of extension blocks: #{bytes[126]}"
    puts "removing extension block"
    bytes = bytes[0..127]
    bytes[126] = 0

    bytes[127] = (0x100-(bytes[0..126].reduce(:+) % 256)) % 256
    puts
    puts "Recalculated checksum: 0x%x" % bytes[127]
    puts "new EDID:\n#{bytes.map{|b|"%02X"%b}.join}"

    Dir.mkdir("DisplayVendorID-%x" % disp["vendorid"]) rescue nil
    filename = "DisplayVendorID-%x/DisplayProductID-%x" % [disp["vendorid"], disp["productid"]]
    puts "Output file: #{Dir.pwd}/#{filename}"
    f = File.open(filename, 'w')
    f.write '<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">'
    f.write "
<dict>
  <key>DisplayProductName</key>
  <string>#{monitor_name} - forced RGB mode (EDID override)</string>
  <key>IODisplayEDID</key>
  <data>#{Base64.encode64(bytes.pack('C*'))}</data>
  <key>DisplayVendorID</key>
  <integer>#{disp["vendorid"]}</integer>
  <key>DisplayProductID</key>
  <integer>#{disp["productid"]}</integer>
</dict>
</plist>"
    f.close
    puts "\n"
end             # displays.each
```

So this is a fairly harmless script, that only reads the monitors that are
connected and prints out a new file.

After copying this file into the right location (instructions in the [section
above](#why-it-happened-and-how-to-fix-it)). Rebooting after this should fix the
fonts.

## Thoughts

### On fonts and monitors

Over time, I've realized that a good font and a large monitor are major quality
of life improvements. They satisfy that impulse in my head to do something for
myself (in terms of improving my day to day life), and also the impulse to
improve and be more productive.

I've been using [Iosevka](https://github.com/be5invis/Iosevka) for two years
now, and it's still one of the best fonts I've seen. It's ignited a love for
narrow width fonts, and a new appreciation for the aesthetics in fonts.

I'm possibly going to be using this for a long long time, despite constantly
being on the lookout for a better font.

### On running arbitrary scripts

Be very careful. Especially, as mentioned, those of you that are not familiar
with programming. The temptation to run a random command that you find on the
internet to solve a niggling problem can be immense, and it can be easy to trust
a random post and run a random command. But it's trivial for someone to craft a
post that looks helpful, but does something malicious.

This only has to bite you once, and the consequences can be disastrous. Be
careful when you do that `curl arbitrary-script.io | sh - ` install command, and
when you run installers, and when you run unknown binaries with sudo.

