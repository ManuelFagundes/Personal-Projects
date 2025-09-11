**Simple Python Transcriber**

This is a straightforward Python script I put together to handle video and podcast transcriptions. It's pretty good for what it is—it grabs the audio, runs it through Google's API, and returns a transcript in a few different formats.

I wrote this because I really enjoy listening and reading news in different languages, and I wanted the transcriptions of some news, so I wrote this. It's not super complicated, but it gets the job done for my intended purposes.

**Features**

*Audio Extraction:* Pulls audio from YouTube and YouTube Music URLs.

*Multi-Format Output:* You can save the transcript as a plain text file (.txt), a Word document (.docx), or a subtitle file (.srt) with timestamps.

*Massive Language Support:* I added a huge list of languages—pretty much all the major ones by native and non-native speakers, plus the G20 countries. If the language isn't on the list, you might need to add it, but there's a good chance it's already there.

*Handles Long Videos:* I bumped up the API timeout, so it should handle even longer content without giving you that annoying "timeout" error. 

**Before You Run**

*To get this script working, you need a few things set up:*

*Python Libraries:* You need to install the libraries listed in the script. You can probably just run this: pip install -r requirements.txt. (I'll add the requirements.txt to the repo later, but for now just install the libraries.)

*Google Cloud Project:* You need a Google Cloud Project with the Speech-to-Text API enabled.

*Google Cloud Storage Bucket:* The script uses a bucket named languagestudies-transcription-audio to temporarily store the audio file for the API. You'll need to make sure this bucket exists in your project.

*Authentication:* You have to set up authentication so the script can talk to your Google Cloud project. The easiest way is to use gcloud auth application-default login and point the GOOGLE_APPLICATION_CREDENTIALS environment variable to your key file.

**How to Use**

Super simple. Just run the script from your terminal:

python your_script_name.py

It will then ask you for three things:

The URL of the video or podcast.

The language of the audio (e.g., "danish", "japanese", "english", "bengali").

The format you want for the output (txt, docx, or srt).

After that, just let it do its thing.

**Troubleshooting**

*Timeout Errors:* If you get a timeout error, it's probably because the video is really long and the transcription is taking a while. I've already increased the timeout, so it shouldn't happen, but if it does, you can increase the timeout again in the transcribe_with_timestamps function. Just change the 1800 to a bigger number.

*"No text was transcribed" Error:* This means the API didn't detect any human speech in the audio. It's usually because the video is just music, sound effects, or something without any talking, or a voice over.

**P.S.: Yes, I know the code's not heavy on comments. But hey, it works, right?**
