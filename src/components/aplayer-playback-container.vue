<template>
  <div class="aplayer-playback-container-wrap">
    <div class="player-controls">
      <div class="playback-controls">
        <AplayerIcon icon="shuffle" />
        <AplayerIcon icon="backward" />
        <AplayerIcon
          :icon="playIcons"
          @click="emitPlayStatus"
          :key="playIcons"
        />
        <AplayerIcon icon="forward" />
        <AplayerIcon icon="repeat" />
      </div>
    </div>
    <div class="player-info-container">
      <APlayBackInfo
        :currentMusicData="props.currentMusicData"
        @aplayerLyricStatus="handleAplayerLyricStatus"
        @updateCurrentTime="handleUpdateCurrentTime"
      />
    </div>
    <div class="player-meta-controls">
      <APlayerIcon
        :icon="handleVolumeIcon"
        className="volume"
        :key="handleVolumeIcon"
        @click="volumeValue = 0"
      />
      <input
        type="range"
        class="volume-progress"
        ref="volumeRefs"
        v-model="volumeValue"
        min="0"
        max="100"
      />
      <div
        :class="`queue-button-wrap ${
          isExpandPlayList ? 'queue-button-active' : ''
        }`"
      >
        <APlayerIcon
          icon="queue-button"
          className="queue-button"
          @click="sendPlayListStatus"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
//vue
import { inject, ref, watch } from "vue";
//components
import AplayerIcon from "./aplayer-icon.vue";
import APlayBackInfo from "./aplayer-playback-info.vue";
import APlayerIcon from "./aplayer-icon.vue";
//types
import { IAplayerData } from "../types/types";

//hooks
import { useVolumeIcons } from "../hooks/useVolumeIcons";
import { useChangePlayIcons } from "../hooks/useAudioControlsIcons";

const props = defineProps<IAplayerData>();
const emit = defineEmits([
  "playListStatus",
  "changePlayStatus",
  "aplayerLyricStatus",
  "updateCurrentTime",
  "updateVolume",
]);

//provide inject
const injectPlayStatus = inject("playStatus");
const { playIcons } = useChangePlayIcons(injectPlayStatus);
const volumeRefs = ref();
const defaultVolume = inject<number>("defaultVolume");

const { handleVolumeIcon, volumeValue } = useVolumeIcons(
  volumeRefs,
  defaultVolume!
);
watch(volumeValue, (newValue: any) => {
  emit("updateVolume", newValue);
});
let isExpandPlayList = ref(false);
const sendPlayListStatus = () => {
  isExpandPlayList.value = !isExpandPlayList.value;
  emit("playListStatus", isExpandPlayList.value);
};
const emitPlayStatus = () => {
  emit("changePlayStatus", playIcons.value);
};
const handleAplayerLyricStatus = (value: boolean) => {
  emit("aplayerLyricStatus", value);
};
const handleUpdateCurrentTime = (value: any) => {
  emit("updateCurrentTime", value);
};
</script>

<style scoped>
.aplayer-playback-container-wrap {
  height: 54px;
  width: 100vw;
  background-color: var(--aplayer-pageBG);
  border-top: 1px solid var(--aplayer-list-border-color);
  display: flex;
  position: absolute;
  bottom: 0;
}
.aplayer-playback-container-wrap:hover::v-deep(
    .progress::-webkit-slider-thumb,
    .progress ::-moz-range-thumb
  ) {
  opacity: 1;
}
.aplayer-playback-container-wrap > div {
  display: flex;
  justify-content: center;
  align-items: center;
}
.player-controls {
  flex: 1;
}
.player-info-container {
  flex: 2;
}
.player-meta-controls {
  flex: 1;
}
::v-deep(.icon-volume) {
  fill: #707070;
  width: 27px;
  height: 23px;
}
.volume,
.queue-button {
  fill: #707070;
  width: 27px;
  height: 23px;
  cursor: pointer;
  transition: fill 0.2s;
}
.queue-button-wrap {
  margin-left: 5px;
  border-radius: 4px;
  width: 32px;
  height: 28px;
  display: flex;
  justify-content: center;
  align-items: center;
  /* background-color: #6c6c6c; */
}
.queue-button-active {
  background-color: #6c6c6c;
}
.queue-button-active .queue-button::v-deep(.aplayer-icon-fill) {
  fill: #ffffff;
}
.queue-button-active .icon:hover ::v-deep(.aplayer-icon-fill) {
  fill: #ffffff;
}
.volume-progress {
  width: 70px;
  height: 3px;
  cursor: pointer;
  -webkit-appearance: none;
  -moz-appearance: none;
  background: linear-gradient(
    to right,
    #7f7f7f 0%,
    #7f7f7f var(--aplayer-volumeProgress, 0%),
    #e5e5e5 var(--aplayer-volumeProgress, 0%),
    #e5e5e5 100%
  );
}
.volume-progress::-webkit-slider-thumb {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  width: 15px;
  height: 15px;
  background-color: #808080;
  border: 1px solid #fff;
  border-radius: 50%;
  cursor: pointer;
  z-index: 1;
  outline: none;
}

.volume-progress ::-moz-range-thumb {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  width: 15px;
  height: 15px;
  background-color: #808080;
  border: 1px solid #fff;
  border-radius: 50%;
  cursor: pointer;
  z-index: 1;
  outline: none;
}
::v-deep(.aplayer-icon-fill) {
  transition: fill 0.2s;
}

.playback-controls {
  display: flex;
}
.icon {
  width: 32px;
  height: 28px;
  cursor: pointer;
}
::v-deep(.aplayer-icon-fill) {
  fill: #707070;
  transition: fill 0.2s;
}
.icon:hover ::v-deep(.aplayer-icon-fill) {
  fill: #1f1f1f;
}
@media screen and (max-width: 760px) {
  .icon-shuffle,
  .icon-backward,
  .icon-repeat,
  .volume,
  .volume-progress {
    display: none;
  }
  .player-info-container {
    flex: 5;
  }
}
@media screen and (max-width: 460px) {
  ::v-deep(.currentTime) {
    display: none;
  }
}
</style>
