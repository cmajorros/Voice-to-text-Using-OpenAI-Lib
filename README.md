# Voice-to-text-Using-OpenAI-Lib

```markdown
# YouTube Video Audio Transcription

This project provides a pipeline for extracting audio from YouTube videos, converting the audio to text, and saving the transcriptions. The project uses the `yt_dlp` library to extract video URLs and download audio, `pytube` to get video metadata, and `speech_recognition` and `openai-whisper` for audio-to-text conversion.

## Installation

To use this project, you need to install the required libraries:

```sh
pip install pytube yt_dlp SpeechRecognition pandas moviepy openai-whisper
```

## Usage

### Step 1: Extract Video URLs from a YouTube Channel

The function `get_video_urls(channel_url)` extracts video URLs from a given YouTube channel URL.

### Step 2: Download Audio from Each Video

The function `download_audio(video_url, output_path="audio")` downloads the audio from the provided video URL and saves it in the specified output directory.

### Step 3: Convert Audio to Text

The function `audio_to_text(audio_file)` converts the downloaded audio file to text using the Google Speech Recognition API.

### Step 4: Process Videos from a Channel

The function `process_channel_videos(channel_url)` processes all videos from a given YouTube channel URL. It downloads the audio, converts it to text, and saves the transcriptions.

### Step 5: Get Video Upload Dates

The function `get_upload_date(video_url)` retrieves the upload date of a video. The main script filters videos based on their upload dates.

### Step 6: Download and Extract Audio from Videos

The function `download_and_extract_audio(video_url, output_path)` downloads the video and extracts audio as an MP3 file.

### Step 7: Convert Audio to Text Using Whisper

The function `audio_to_text(audio_path, model)` uses the Whisper model to convert audio files to text. The transcriptions are saved in the specified output directory.

## Example

```python
if __name__ == "__main__":
    channel_url = "https://www.youtube.com/@Preset-io"
    video_urls = get_video_urls(channel_url)
    
    # Process videos from the channel
    df = process_channel_videos(channel_url)
    
    # Filter videos uploaded in 2023
    data = []
    for url in video_urls:
        date = get_upload_date(url)
        if date and date > pd.Timestamp('2023-01-01') and date < pd.Timestamp('2024-01-01'):
            data.append((url, date))
    
    df = pd.DataFrame(data, columns=['URL', 'Upload Date'])
    print(df)
    
    # Download and extract audio from filtered videos
    download_dir = 'downloads'
    os.makedirs(download_dir, exist_ok=True)
    for index, row in df.iterrows():
        video_url = row['URL']
        download_and_extract_audio(video_url, download_dir)
    
    # Convert extracted audio to text using Whisper
    model = whisper.load_model('base')
    directory_path = 'downloads/'
    output_directory = 'content/speech_conversion/'
    os.makedirs(output_directory, exist_ok=True)
    
    for filename in os.listdir(directory_path):
        if filename.endswith(".mp3"):
            audio_path = os.path.join(directory_path, filename)
            text = audio_to_text(audio_path, model)
            output_path = os.path.join(output_directory, f"{os.path.splitext(filename)[0]}.txt")
            with open(output_path, 'w') as file:
                file.write(text)
            print(f"Converted {filename} to text and saved to {output_path}")
```

## Notes

- Ensure that FFmpeg is installed on your system for audio processing.
