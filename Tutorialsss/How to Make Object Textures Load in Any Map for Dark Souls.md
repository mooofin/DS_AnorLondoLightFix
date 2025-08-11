### **How to Get Object Textures to Load in Any Map (DS1)**

#### **So, What's the Big Idea?**

Ever tried to place a cool piece of furniture or a unique statue from one area of Dark Souls into another, only to have it show up as a weird, untextured blob? It’s a common headache. That’s because most objects are hard-wired to only look for their textures in the specific map they originally belong to.

This guide will show you how to fix that. We’re going to "teach" an object to carry its own textures with it. That way, it doesn't matter if you place it in Anor Londo or Blighttown—it will always look right. This trick works for both the original *Prepare to Die Edition* and the *Remastered* version. It's a bit of a process, but totally worth it for getting your world looking just right.

#### **What You'll Need**

Before we dive in, make sure you have these tools ready to go. They're essential for any serious Dark Souls modding.

  * **Yabber:** Your go-to tool for unpacking and repacking the game's archive files.
  * **FLVER Editor:** This lets you look at the 3D models and, more importantly for us, see what textures they use.
  * **An Unpacked Game:** You'll need a full copy of the game's files unpacked into a folder.

-----

### **The Walkthrough**

#### **Step 1: The Detective Work - Finding the Texture Clues**

First, we need to figure out exactly which textures our object uses.

1.  **Find Your Object:** Head into your unpacked game files and navigate to the `\obj\` folder. Find the `.objbnd` file for the object you want to work with. For this example, let's pretend we're using `o1321.objbnd`.

2.  **Pop it Open:** Drag the `.objbnd` file onto Yabber to unpack it into its own folder.

3.  **Check the Materials:** Inside that new folder, you'll find a `.flver` file—that's the actual 3D model. Open it up with FLVER Editor.

4.  **Look for the Paths:** In the FLVER Editor window, click on the **Material** tab. You'll see a list of materials the model uses. Click the first one (`[0]`) and hit **Edit**.

5.  **Take Some Notes:** A new window will pop up showing you paths to texture files. These paths are the clues we're looking for\! They tell you which map folder the textures are in (like `\m10\` for the Depths/Undead Burg) and the name of the texture itself. Jot down the map folder and the file names.

6.  **Rinse and Repeat:** You'll need to do this for *every single material* in the list to make sure you have a complete list of all the textures the object needs. It can be a bit tedious, but it's crucial\!

#### **Step 2: The Heist - Grabbing the Textures**

Now that we know what we're looking for, it's time to go get it.

1.  **Head to the Map Folder:** Go to the `\map\` directory in your unpacked game files and find the folder for the map you noted down earlier (e.g., `\m10\`).

2.  **Unpack the Texture Stash:** Inside, you'll see a bunch of `mXX_000X.tpfbhd` files. These are the texture packs for the map. To make life easy, I usually just unpack all of them with Yabber so I don't have to guess which one holds the files I need.

3.  **Find Your Targets:** Sift through the folders Yabber just created. You're looking for the `.tpf` files with the same names you wrote down. Once you find them, unpack each of those `.tpf` files with Yabber, too. This will finally give you the raw `.dds` texture file and a little `.xml` file that goes with it.

#### **Step 3: The Assembly - Building a Custom Texture Pack**

Okay, you should now have a bunch of little folders, each containing a `.dds` file and an `.xml`. Let's bundle them all up into one neat package.

1.  **Make a New Home:** Create a new, empty folder. Give it a sensible name, like `o1321-textures`, so you know what it is.

2.  **Move Everyone In:** Copy all the `.dds` files you just extracted and paste them into your new folder.

3.  **Combine the Blueprints:** Now for the `.xml` files. Think of them as a "table of contents" for the textures. We need to merge them into one master list.

      * Pick one `_yabber-tpf.xml` and copy it into your new folder.
      * Open it up. Now, open up all the *other* `.xml` files one by one.
      * From each of the other files, copy the whole `<texture>...</texture>` block and paste it into your main `.xml` file, right before the `</textures>` tag.

    Here is an example of what your `_yabber-tpf.xml` file may look like after all these steps are completed (dcx will be in the name if done on DS:R):

    ```xml
    <?xml version="1.0" encoding="utf-8"?><tpf>
      <filename>o1321.tpf.dcx</filename>
      <compression>DarkSouls1</compression>
      <encoding>0x02</encoding>
      <flag2>0x03</flag2>
      <textures>
        <texture>
          <name>m10_01_wood_02.dds</name>
          <format>0x01</format>
          <flags1>0x00</flags1>
          <flags2>0x00000000</flags2>
        </texture>
        <texture>
          <name>m10_01_wood_02_s.dds</name>
          <format>0x01</format>
          <flags1>0x00</flags1>
          <flags2>0x00000000</flags2>
        </texture>
        <texture>
          <name>m10_01_wood_02_n.dds</name>
          <format>0x24</format>
          <flags1>0x00</flags1>
          <flags2>0x00000000</flags2>
        </texture>
        <texture>
          <name>m10_01_arrow_01.dds</name>
          <format>0x01</format>
          <flags1>0x00</flags1>
          <flags2>0x00000000</flags2>
        </texture>
        <texture>
          <name>m10_01_arrow_01_s.dds</name>
          <format>0x01</format>
          <flags1>0x00</flags1>
          <flags2>0x00000000</flags2>
        </texture>
        <texture>
          <name>m10_01_arrow_01_n.dds</name>
          <format>0x24</format>
          <flags1>0x00</flags1>
          <flags2>0x00000000</flags2>
        </texture>
      </textures></tpf>
    ```

4.  **Pack it Up:** Drag this new folder onto Yabber to pack it into a single `.tpf` file.

#### **Step 4: The Final Connection - Hooking It All Up**

We're in the home stretch\! All that's left is to tell the object to use our new texture pack.

1.  **Add Your Pack:** Take the new `.tpf` file you just made and move it into the unpacked `.objbnd` folder (the one from Step 1 with the `.flver` file in it).

2.  **Edit the Packing List:** Inside that same folder, find and open the `_yabber-bnd3.xml` (for PTDE) or `_yabber-bnd4.xml` (for Remastered).

3.  **The Magic Number:** This is the most important part of the whole process. We need to add our `.tpf` file to this list, but it needs a special ID. Add a new `<file>` entry, but make sure you set its ID to `100`.

    Why `100`? It's a special ID that basically tells the game, "Hey\! Stop looking in the map files for textures. The ones you need are right here, inside this package\!"

    Your new entry should look like this:

    ```xml
    <file>
      <id>100</id>
      <path>o1321.tpf.dcx</path> </file>
    ```

4.  **Final Repack:** That's it\! Drag the entire `.objbnd` folder (the one you've been working in) back onto Yabber to repack it.

5.  **Victory\!** Take your new, modified `.objbnd` file, drop it into your mod's `obj` folder, and boot up the game. Go ahead and place your object in a completely different map. It should now show up perfectly, with all its textures intact.

Happy modding\!
