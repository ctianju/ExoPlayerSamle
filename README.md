# ExoPlayerSamle
    1.ExoPlayer 简单自定义播放界面，新建一个Layout 布局文件命名为exo_playback_control_view，控件的id不能随便起，要与exoPlayer原来PlaybackControlView的布局控件id，名称一致，布局名称不一定是exo_playback_control_view，不过需要在SimpleExoPlayerView控件中添加一个： 
app:controller_layout_id="@layout/id" ，指定control的布局；

    2.如果需要详细的自定义播放界面，需要重写SimpleExoPlayerView，和PlaybackControlView。
