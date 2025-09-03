# 📜 JavaScript Logic (Music Player)

This document explains only the **JavaScript functionality** of the **music player**.  

## 🔑 1. Variables & Elements

```js
const playlistSongs = document.getElementById("playlist-songs");
const playButton = document.getElementById("play");
const pauseButton = document.getElementById("pause");
const nextButton = document.getElementById("next");
const previousButton = document.getElementById("previous");
const shuffleButton = document.getElementById("shuffle");
```

- These lines select HTML elements from the page by their `id`.
- `document.getElementById()` returns the first element with the matching id.
- Example: `playlistSongs` will refer to the `<ul>` or `<div>` that will contain all the songs.

## 🎵 2. Defining all songs

```js
const allSongs = [
  { id: 0, title: "Scratching The Surface", artist: "Quincy Larson", duration: "4:25", src: "..." },
  { id: 1, title: "Can't Stay Down", artist: "Quincy Larson", duration: "4:15", src: "..." },
  // ...more songs
];
```

- This is an **array of song** objects.

- Each object represents a song with properties:

      - `id` – unique identifier for the song

      - `title` – song name

      - `artist` – artist name

      - `duration` – song length as a string
  
      - `src` – URL of the audio file
  

## 🔊 3. Creating an Audio object and user data

```js
const audio = new Audio();
let userData = {
  songs: [...allSongs],
  currentSong: null,
  songCurrentTime: 0,
};

```

- `new Audio()` creates an HTML audio element in JavaScript.

- `userData` stores the user’s state:

    - `songs` – the playlist (copied from `allSongs`)

    - `currentSong` – currently playing song (starts as `null`)

    - `songCurrentTime` – remembers where playback was paused

## ▶️ 4. Function to play a song

```js
const playSong = (id) => {
  const song = userData?.songs.find((song) => song.id === id);
  audio.src = song.src;
  audio.title = song.title;

  if (userData?.currentSong === null || userData?.currentSong.id !== song.id) {
    audio.currentTime = 0;
  } else {
    audio.currentTime = userData?.songCurrentTime;
  }

  userData.currentSong = song;

  audio.play();
  playButton.classList.add("playing");
};

```

- Finds the song by its `id` using `.find()`.

- Sets the audio source (`src`) and title.

- If it’s a new song → start from the beginning (`currentTime = 0`).

- If it’s the same song → resume from where it left off.

- Updates `userData.currentSong`.

- Starts playback with `audio.play()`.

- Adds a CSS class `"playing"` to the play button for styling.

## 📝 5. Function to pause a song

```js
const pauseSong = () => {
  userData.songCurrentTime = audio.currentTime;
  playButton.classList.remove("playing");
  audio.pause();
};

```

- Saves the current playback time into `userData.songCurrentTime`.

- Removes the `"playing"` CSS class.

- Calls `audio.pause()` to stop playback.

## 🔤 6. Function to render songs in HTML
```js
const renderSongs = (array) => {
  const songsHTML = array
    .map((song)=> {
      return `
      <li id="song-${song.id}" class="playlist-song">
        <button class="playlist-song-info" onclick="playSong(${song.id})">
          <span class="playlist-song-title">${song.title}</span>
          <span class="playlist-song-artist">${song.artist}</span>
          <span class="playlist-song-duration">${song.duration}</span>
        </button>
        <button class="playlist-song-delete" aria-label="Delete ${song.title}">
          <svg>...</svg>
        </button>
      </li>
      `;
    })
    .join("");

  playlistSongs.innerHTML = songsHTML;
};

- Builds the playlist in the browser.

- Each song is rendered as an `<li>`.

- The info button calls `playSong(id)` when clicked.

- Displays title, artist, and duration.

```

## 🎚️ 7. Function to sort songs alphabetically

```js
const sortSongs = () => {
  userData?.songs.sort((a, b) => {
    if (a.title < b.title) return -1;
    if (a.title > b.title) return 1;
    return 0;
  });
  return userData?.songs;
};

```
- Sorts the songs in A–Z order by title.

- Returns the sorted array for rendering.

## 📍 8. Get index of current song

```js
const getCurrentSongIndex = () =>
userData?.songs.indexOf(userData?.currentSong);

```

## 🖱️ 9. Next and Previous buttons

```js
nextButton.addEventListener("click", () => {
  const currentIndex = getCurrentSongIndex();
  if (currentIndex !== -1 && currentIndex < userData.songs.length - 1) {
    playSong(userData.songs[currentIndex + 1].id);
  }
});

previousButton.addEventListener("click", () => {
  const currentIndex = getCurrentSongIndex();
  if (currentIndex > 0) {
    playSong(userData.songs[currentIndex - 1].id);
  }
});

```
**Next button** → plays the next song in the playlist.

**Previous button** → plays the previous song.

Uses `getCurrentSongIndex()` to check where you are in the list.

## 📑 10. Play & Pause button listeners

```js
playButton.addEventListener("click", () => {
  if (userData?.currentSong === null) {
    playSong(userData?.songs[0].id);
  } else {
    playSong(userData?.currentSong.id);
  }
});

pauseButton.addEventListener("click", pauseSong);
```
**Play button:**

  - If no song is playing → start the first one.

  - Otherwise → resume the current one.

**Pause button** calls pauseSong().

## 🏁 11. Initial rendering

```js
renderSongs(sortSongs());

```
- Sorts songs alphabetically and shows them in the playlist when the app first loads.
