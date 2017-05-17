---
name: ExoPlayer
---

To play videos and audio files (local or remote) Google has created an open source project [ExoPlayer](https://github.com/google/ExoPlayer) that provides *more* functionality then the `MediaPlayer` that comes with Android.

For streaming videos and audio, I highly recommend ExoPlayer. It is a beast of a project, but you can also use a fraction of it's power to get it up and running for a tradional mp4 streaming video player.

# Install

Via gradle of course.

```
def latestExoplayer = 'r2.4.0'
compile "com.google.android.exoplayer:exoplayer-core:$latestExoplayer"
//compile "com.google.android.exoplayer:exoplayer-hls:$latestExoplayer"
compile "com.google.android.exoplayer:exoplayer-ui:$latestExoplayer"
//compile "com.google.android.exoplayer:exoplayer-smoothstreaming:$latestExoplayer"
//compile "com.google.android.exoplayer:exoplayer-dash:$latestExoplayer"
```

I have commented out a few of the gradle plugins here because with basic mp4 streaming playback, I only need a couple of these.

# Stream mp4 videos

I got this code from the ExoPlayer [developer guide](https://google.github.io/ExoPlayer/guide.html) where it gives you this basic code to get up and running with mp4. If you need to use something else besides play mp4 videos, the docs will help you. Also, I recommend checking out the ExoPlayer demo app (located in the [GitHub repo](https://github.com/google/ExoPlayer) for ExoPlayer) as it has a file, `PlayerActivity` that you can check out to play many types of video files. There is a guide for the demo as well [here](https://google.github.io/ExoPlayer/demo-application.html)

```
import com.google.android.exoplayer2.ExoPlaybackException;
import com.google.android.exoplayer2.ExoPlayer;
import com.google.android.exoplayer2.PlaybackParameters;
import com.google.android.exoplayer2.SimpleExoPlayer;
import com.google.android.exoplayer2.Timeline;
import com.google.android.exoplayer2.source.TrackGroupArray;
import com.google.android.exoplayer2.trackselection.TrackSelectionArray;
import com.google.android.exoplayer2.ui.SimpleExoPlayerView;

public class ViewMediaActivity extends BaseActivity implements ExoPlayer.EventListener {

    private SimpleExoPlayerView mVideoPlayer;
    private SimpleExoPlayer mExoPlayer;

    ...
    VideoPlayer = (SimpleExoPlayerView) findViewById(R.id.media_video_player);
    ...

    @Override protected void onStop() {
        super.onStop();

        if (mExoPlayer != null) {
            mExoPlayer.release();
        }
    }    

    ...
    // When you are ready to load the Uri and play it to the user.
    mExoPlayer = ExoPlayerUtil.getMp4StreamingExoPlayer(context, mediaUri);
    mExoPlayer.addListener(this);
    mVideoPlayer.setPlayer(mExoPlayer);
    ...

    @Override public void onTimelineChanged(Timeline timeline, Object manifest) {
    }

    @Override
    public void onTracksChanged(TrackGroupArray trackGroups, TrackSelectionArray trackSelections) {
    }

    @Override public void onLoadingChanged(boolean isLoading) {
    }

    @Override public void onPlayerStateChanged(boolean playWhenReady, int playbackState) {
    }

    @Override public void onPlayerError(ExoPlaybackException error) {
        DialogUtil.getSimpleOkDialog(this, R.string.error, R.string.error_playing_video)
                .getBuilder()
                .onNegative(new MaterialDialog.SingleButtonCallback() {
                    @Override
                    public void onClick(@NonNull MaterialDialog dialog, @NonNull DialogAction which) {
                        setupCurrentlySelectedMedia();
                    }
                })
                .negativeText(R.string.retry)
                .build()
                .show();
    }

    @Override public void onPositionDiscontinuity() {
    }

    @Override public void onPlaybackParametersChanged(PlaybackParameters playbackParameters) {
    }

}
```

And then I created a `ExoPlayerUtil` to make it easier to create the boilerplate:

```
import android.content.Context
import android.net.Uri
import com.google.android.exoplayer2.ExoPlayerFactory
import com.google.android.exoplayer2.SimpleExoPlayer
import com.google.android.exoplayer2.extractor.DefaultExtractorsFactory
import com.google.android.exoplayer2.source.ExtractorMediaSource
import com.google.android.exoplayer2.trackselection.AdaptiveTrackSelection
import com.google.android.exoplayer2.trackselection.DefaultTrackSelector
import com.google.android.exoplayer2.upstream.DefaultBandwidthMeter
import com.google.android.exoplayer2.upstream.DefaultDataSourceFactory
import com.google.android.exoplayer2.util.Util

class ExoPlayerUtil {

    companion object {

        fun getMp4StreamingExoPlayer(context: Context, videoUri: Uri): SimpleExoPlayer {
            val bandwidthMeter = DefaultBandwidthMeter()
            val videoTrackSelectionFactory = AdaptiveTrackSelection.Factory(bandwidthMeter)
            val trackSelector = DefaultTrackSelector(videoTrackSelectionFactory)

            val dataSourceFactory = DefaultDataSourceFactory(context, Util.getUserAgent(context, "NameOfYourAppHere"), bandwidthMeter)
            val extractorsFactory = DefaultExtractorsFactory()

            // using the ExtractorMediaSource because it is best for mp4 videos.
            val videoSource = ExtractorMediaSource(videoUri, dataSourceFactory, extractorsFactory, null, null)

            val videoPlayer = ExoPlayerFactory.newSimpleInstance(context, trackSelector)
            videoPlayer.prepare(videoSource)
            videoPlayer.playWhenReady = true

            return videoPlayer
        }

    }

}
```
