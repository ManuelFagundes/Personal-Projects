import yt_dlp
import os
import docx
import pysubs2
from datetime import timedelta
from google.cloud import speech, storage

# A massive list of languages so we can handle pretty much anything.
# This includes the top 20 languages by native and non-native speakers,
# plus the official ones from the G20 countries.
LANGUAGE_MAP = {
    # Top 20 Languages by Native Speakers
    "mandarin chinese": "zh-CN",
    "spanish": "es-ES",
    "english": "en-US",
    "hindi": "hi-IN",
    "bengali": "bn-IN",
    "portuguese": "pt-PT",
    "russian": "ru-RU",
    "japanese": "ja-JP",
    "punjabi": "pa-IN",
    "marathi": "mr-IN",
    "telugu": "te-IN",
    "wu chinese": "zh-CN",  # Using Mandarin code, since it's the closest thing
    "german": "de-DE",
    "korean": "ko-KR",
    "french": "fr-FR",
    "vietnamese": "vi-VN",
    "tamil": "ta-IN",
    "urdu": "ur-PK",
    "javanese": "jv-ID",
    "italian": "it-IT",
    
    # Top 20 Languages by Non-Native Speakers (some of these overlap)
    "arabic": "ar-SA",
    "malay": "ms-MY",
    "farsi": "fa-IR", # Persian
    "hausa": "ha-NG",
    "swahili": "sw-TZ",
    "turkish": "tr-TR",
    
    # G20 Languages (yep, more overlap)
    "indonesian": "id-ID",
    "brazilian portuguese": "pt-BR",
    "mexican spanish": "es-MX",
    "dutch": "nl-NL",
    "afrikaans": "af-ZA",
    "south african english": "en-ZA",
    "saudi arabic": "ar-SA", # This is the official one for Saudi Arabia
    
    # The others we've already added for convenience
    "danish": "da-DK",
    "norwegian": "nb-NO",
}

def download_audio(url):
    """Grabs the audio from a URL using yt-dlp."""
    ydl_opts = {
        'format': 'bestaudio/best',
        'postprocessors': [{
            'key': 'FFmpegExtractAudio',
            'preferredcodec': 'mp3',
            'preferredquality': '192',
        }],
        'outtmpl': 'downloaded_audio_%(id)s.%(ext)s',
        'quiet': True,
    }
    try:
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info_dict = ydl.extract_info(url, download=True)
            audio_filename = ydl.prepare_filename(info_dict).replace('.webm', '.mp3')
            print(f"⭐ Audio downloaded to: {audio_filename}")
            return audio_filename
    except Exception as e:
        print(f"➖ Whoops, couldn't download the audio: {e}")
        return None

def upload_to_gcs(bucket_name, source_file_name, destination_blob_name):
    """Puts a file into a Google Cloud Storage bucket."""
    try:
        storage_client = storage.Client()
        bucket = storage_client.bucket(bucket_name)
        blob = bucket.blob(destination_blob_name)
        blob.upload_from_filename(source_file_name)
        print(f"⭐ File {source_file_name} uploaded to GCS. Nice.")
        return f"gs://{bucket_name}/{destination_blob_name}"
    except Exception as e:
        print(f"➖ Didn't work. Failed to upload to GCS: {e}")
        return None

def delete_from_gcs(bucket_name, blob_name):
    """Wipes a file from the Google Cloud Storage bucket."""
    try:
        storage_client = storage.Client()
        bucket = storage_client.bucket(bucket_name)
        blob = bucket.blob(blob_name)
        blob.delete()
        print(f"🗑️ Cleaned up the GCS file: {blob_name}")
    except Exception as e:
        print(f"➖ Couldn't delete the GCS file. Oh well: {e}")

def transcribe_with_timestamps(gcs_uri, language_code):
    """Transcribes the audio, making sure to get the timing for the subtitles."""
    client = speech.SpeechClient()
    audio = speech.RecognitionAudio(uri=gcs_uri)
    config = speech.RecognitionConfig(
        encoding=speech.RecognitionConfig.AudioEncoding.MP3,
        language_code=language_code,
        enable_automatic_punctuation=True,
        enable_word_time_offsets=True,
    )
    print("🎙️ Starting transcription... this might take a minute...")
    operation = client.long_running_recognize(config=config, audio=audio)
    
    # Increased the timeout to 30 minutes (1800 seconds)
    print("⏳ Waiting for the transcription to finish... (This could take a while, just hang in there)")
    response = operation.result(timeout=1800)

    if not response.results:
        print("➖ Huh. No text was transcribed. Nothing to work with here.")
        return None

    print("⭐ All done. Transcription complete.")
    return response.results

def save_to_txt(text, filename):
    with open(filename, 'w', encoding='utf-8') as f:
        f.write(text)
    print(f"⭐ Saved the transcription to: {filename}")

def save_to_docx(text, filename):
    document = docx.Document()
    document.add_paragraph(text)
    document.save(filename)
    print(f"⭐ Got it. Saved the transcription to a Word doc: {filename}")

def save_to_srt(results, filename):
    subs = pysubs2.SSAFile()
    current_subs = ""
    start_time = None

    for result in results:
        for word_info in result.alternatives[0].words:
            word = word_info.word
            start_ms = word_info.start_time.total_seconds() * 1000
            end_ms = word_info.end_time.total_seconds() * 1000

            if start_time is None:
                start_time = start_ms

            if len(current_subs.split()) >= 10:
                subs.append(pysubs2.SSAEvent(start=start_time, end=end_ms, text=current_subs.strip()))
                current_subs = ""
                start_time = None
            
            current_subs += word + " "

    if current_subs:
        subs.append(pysubs2.SSAEvent(start=start_time, end=end_ms, text=current_subs.strip()))

    subs.save(filename)
    print(f"⭐ Subtitle file saved! You'll find it at: {filename}")

def run_program():
    """The main bit of the program that runs everything."""
    print("Welcome to the Gemini Podcast/Video Transcriber! ✍️")
    print("-" * 50)
    
    url = input("🔗 Please give me the URL: ")
    lang_name = input("🗣️ What language is it? (e.g., 'Portuguese Brazil', 'Danish'): ").lower()
    output_format = input("💾 What format do you want? (txt, docx, or srt): ").lower()

    if lang_name not in LANGUAGE_MAP:
        print(f"➖ Whoa, I don't know that language. Try picking from this list instead: {', '.join(LANGUAGE_MAP.keys())}")
        return
    
    if output_format not in ["txt", "docx", "srt"]:
        print(f"➖ That's not a format I know. Stick to txt, docx, or srt.")
        return

    original_lang = LANGUAGE_MAP[lang_name]
    
    bucket_name = "languagestudies-transcription-audio"
    local_audio_path = None
    gcs_audio_path = None
    
    try:
        # 1. First, we gotta download the audio
        local_audio_path = download_audio(url)
        if not local_audio_path: return

        # 2. Then, we chuck it up to Google Cloud
        file_name = os.path.basename(local_audio_path)
        gcs_audio_path = upload_to_gcs(bucket_name, local_audio_path, file_name)
        if not gcs_audio_path: return

        # 3. Time to transcribe with the timestamps
        transcription_results = transcribe_with_timestamps(gcs_audio_path, original_lang)
        if not transcription_results:
            return

        # 4. Lastly, we save it however you want
        base_filename = os.path.splitext(file_name)[0]
        
        if output_format == "txt" or output_format == "docx":
            full_text = " ".join([word_info.word for result in transcription_results for word_info in result.alternatives[0].words])
            if output_format == "txt":
                save_to_txt(full_text, f"{base_filename}.txt")
            elif output_format == "docx":
                save_to_docx(full_text, f"{base_filename}.docx")
        
        elif output_format == "srt":
            save_to_srt(transcription_results, f"{base_filename}.srt")

    except Exception as e:
        print(f"➖ Something went wrong: {e}")
    finally:
        # Clean up local file
        if local_audio_path and os.path.exists(local_audio_path):
            os.remove(local_audio_path)
            print(f"🗑️ Cleaned up the local file: {local_audio_path}")

        # Clean up GCS file
        if gcs_audio_path:
            blob_name = os.path.basename(gcs_audio_path)
            delete_from_gcs(bucket_name, blob_name)

if __name__ == "__main__":
    run_program()
