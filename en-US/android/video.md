---
name: Video
---

# Play videos local or remote in app

I recommend the ExoPlayer video player provided by Google. I have a `exoplayer` doc here in the wiki to learn about how to use it.

# Play YouTube video in Android app

There are 2 methods to doing this. (*Note: ExoPlayer cannot play YouTube videos. View the `exoplayer` doc here in the wiki to learn why not.*):

1. Create an intent that sends the YouTube video link to the YouTube app to play (not documented here as I have not done this yet).

2. Use the YouTube Android Player SDK into your app that I document below:

The official docs for how to add a YouTube video player into your app is [here](https://developers.google.com/youtube/android/player/) which *might* be more up to date then this.

* [Download the SDK](https://developers.google.com/youtube/android/player/downloads/) to your Android project. Yeah, it's a jar. Not gradle. Lame.

* Follow [these directions](https://developers.google.com/youtube/android/player/register) on how to create an API key for using YouTube in your app.

* Add the player code to your app project. Here, I am going to use the `YouTubePlayerSupportFragment` which is a simple way to add a video player into your app. I enjoy this better then using `YouTubePlayerView` directly as it handles a lot of boilerplate for you *and* you do not need to extend a special `YouTubeBaseActivity`.

Create a fragment container in your layout:

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:app="http://schemas.android.com/apk/res-auto"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:background="@android:color/black">    

    <FrameLayout
        android:id="@+id/foo_youtube_videoplayer_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingTop="?attr/actionBarSize"/>

</RelativeLayout>
```

**Warning: Make sure that your container has a high Z-Index and is not covered up by any views at all. If the YouTubePlayerSupportFragment is covered up in any way the video will not play.**

Add your YouTube API key you created to a string resources file. I like to create a file `creds.xml` to hold onto the strings:

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="youtube_player_android_api_cred_key">key_here</string>
</resources>
```

Now, in your fragment or Activity:

```
class FooVideoFragment : BaseKotlinFragment(), YouTubePlayer.OnInitializedListener {

    companion object {
        private val YOUTUBE_VIDEO_PLAYER_FRAGMENT_CONTAINER_TAG = "youtubeVideoPlayerFragmentContainerTag.${javaClass.simpleName}"

        private val RECOVERY_DIALOG_REQUEST = 0
    }

    private var youtubeVideoPlayerFragment: YouTubePlayerSupportFragment = YouTubePlayerSupportFragment()
    private val youtubeVideoUrl: String = "https://www.youtube.com/watch?v=_CeL5o_fyTQ"

    override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        val view: View = inflater!!.inflate(R.layout.fragment_foo_video, container, false)        

        if (savedInstanceState == null) {
            childFragmentManager.beginTransaction()
                    .add(R.id.foo_youtube_videoplayer_container, youtubeVideoPlayerFragment, YOUTUBE_VIDEO_PLAYER_FRAGMENT_CONTAINER_TAG)
                    .commit()
        } else {
            youtubeVideoPlayerFragment = childFragmentManager.findFragmentByTag(YOUTUBE_VIDEO_PLAYER_FRAGMENT_CONTAINER_TAG) as YouTubePlayerSupportFragment
        }

        return view
    }    

    private fun hideYouTubeVideoPlayer() {
        youtubeVideoPlayerFragment.view!!.visibility = View.GONE
    }

    private fun showYouTubeVideoPlayer() {
        youtubeVideoPlayerFragment.view!!.visibility = View.VISIBLE
    }   

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)

        if (requestCode == RECOVERY_DIALOG_REQUEST) {
            youtubeVideoPlayerFragment.initialize(getString(R.string.youtube_player_android_api_cred_key), this)
        }
    }     

    //  Call me whenever you're ready to play the video.
    private fun playVideo() {
        youtubeVideoPlayerFragment.initialize(getString(R.string.youtube_player_android_api_cred_key), this)
    }

    override fun onInitializationFailure(provider: YouTubePlayer.Provider?, youTubeInitializationResult: YouTubeInitializationResult?) {
        LogUtil.error(RuntimeException("YouTube playing error video ${youtubeVideoUrl}: error: ${youTubeInitializationResult.toString()}"))
        if (youTubeInitializationResult!!.isUserRecoverableError) {
            youTubeInitializationResult.getErrorDialog(activity, RECOVERY_DIALOG_REQUEST).show()
        } else {
            Toast.makeText(activity, youTubeInitializationResult.toString(), Toast.LENGTH_LONG).show()
        }
    }

    override fun onInitializationSuccess(provider: YouTubePlayer.Provider?, player: YouTubePlayer?, wasRestored: Boolean) {
        if (!wasRestored) {
            player!!.cueVideo(youtubeVideoUrl.split("=")[1]) // Yeah, you need to only send the hash at the end.
        }
    }

}
```
