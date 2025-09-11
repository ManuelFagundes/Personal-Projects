# **Simple Python Transcriber**

This is a straightforward Python script I put together to handle video and podcast transcriptions. It's pretty good for what it is—it grabs the audio, runs it through Google's API, and returns a transcript in a few different formats.

I wrote this because I really enjoy listening and reading news in different languages, and I wanted the transcriptions of some news, so I wrote this. It's not super complicated, but it gets the job done for my intended purposes.

# **Features**

- *Audio Extraction:* Pulls audio from YouTube and YouTube Music URLs.

- *Multi-Format Output:* You can save the transcript as a plain text file (.txt), a Word document (.docx), or a subtitle file (.srt) with timestamps.

- *Massive Language Support:* I added a huge list of languages—pretty much all the major ones by native and non-native speakers, plus the G20 countries. If the language isn't on the list, you might need to add it, but there's a good chance it's already there.

- *Handles Long Videos:* I bumped up the API timeout, so it should handle even longer content without giving you that annoying "timeout" error. 

# **Before You Run**

- *To get this script working, you need a few things set up:*

- *Python Libraries:* You need to install the libraries listed in the script. You can probably just run this: pip install -r requirements.txt. (I'll add the requirements.txt to the repo later, but for now just install the libraries.)

- *Google Cloud Project:* You need a Google Cloud Project with the Speech-to-Text API enabled.

- *Google Cloud Storage Bucket:* The script uses a bucket named languagestudies-transcription-audio to temporarily store the audio file for the API. You'll need to make sure this bucket exists in your project.

- *Authentication:* You have to set up authentication so the script can talk to your Google Cloud project. The easiest way is to use gcloud auth application-default login and point the GOOGLE_APPLICATION_CREDENTIALS environment variable to your key file.

# **How to Use**

- Super simple. Just run the script from your terminal:

```python your_script_name.py```

- It will then ask you for three things:

* The URL of the video or podcast.

* The language of the audio (e.g., "danish", "japanese", "english", "bengali").

* The format you want for the output (txt, docx, or srt).

After that, just let it do its thing.

## **Troubleshooting**

*Timeout Errors:* If you get a timeout error, it's probably because the video is really long and the transcription is taking a while. I've already increased the timeout, so it shouldn't happen, but if it does, you can increase the timeout again in the transcribe_with_timestamps function. Just change the 1800 to a bigger number.

*"No text was transcribed" Error:* This means the API didn't detect any human speech in the audio. It's usually because the video is just music, sound effects, or something without any talking, or a voice over.

**P.S.: Yes, I know the code's not heavy on comments. But hey, it works, right?**


# **For the Totally New**

Welcome! So you've found this cool script and you want to run it on your computer, but you're not sure how? No problem. This guide is for you.

We're going to get this running together, one simple step at a time. Just follow along.

## *Step 1: Get the script onto your computer*

The easiest way to get all the files from GitHub is to download the whole project as a zip file.

On the main page of this project, look for a green button that says "Code".

Click on it, and then click "Download ZIP" from the little menu that pops up.

Save the zip file to a place you can easily find, like your Desktop.

Find the zip file you just downloaded and "unzip" it.

- **On Windows, you can usually right-click and choose "Extract All...".**

- **On a Mac, just double-click the file.**

You'll now have a normal folder on your computer with all the script files inside.

## *Step 2: Open your "command" window*
This might sound a little scary, but it's just a special window where you type commands to your computer. It's how we'll run the script.

On Windows: Go to the Start menu, and type cmd or Command Prompt. Click on the app that appears.

On a Mac: Search for "Terminal" and open that app.

You'll see a dark window with some text in it. Don't worry about the text; you're just a few steps away from running the script.

## *Step 3: Tell your computer where the script is*
In that dark window you just opened, you need to tell your computer to "go to" the folder with the script. You do this with a command called cd.

Type cd (don't forget the space after cd).

Then, drag the folder you unzipped in Step 1 directly into the dark window.

When you do this, your computer will automatically fill in the file path for you.

Press Enter.

The text in the window will change, showing you that you are now "inside" the script's folder.

## *Step 4: Install the extra parts the script needs*
This script can't do its job all by itself. It needs a few extra tools (called "libraries") to work. We've made a list of them in a file called requirements.txt.

In the same dark window, copy and paste this command exactly as you see it and press Enter.

```pip install -r requirements.txt```

Your computer will start downloading and installing everything it needs. You'll see a bunch of text fly by. Just wait until it stops.

## *Step 5: Run the script*
Now you're ready! This is the moment you've been waiting for. In the same window, type this command and press Enter:

```python your_script_name.py```

The script will start asking you for the video link, the language, and the format. Just follow the instructions and you're good to go.

If you get any errors, don't worry. Just copy the error and search for it online. You can also come back here and ask for more help.
