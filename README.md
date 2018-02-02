# ExoPlayerSamle
    1.ExoPlayer 简单自定义播放界面，新建一个Layout 
    布局文件命名为exo_playback_control_view，控件的id不能随便起，
    要与exoPlayer原来PlaybackControlView的布局控件id，名称一致，
    布局名称不一定是exo_playback_control_view，不过需要在SimpleExoPlayerView控件中添加一个： 
    app:controller_layout_id="@layout/id" ，指定control的布局；

    2.如果需要详细的自定义播放界面，需要重写SimpleExoPlayerView，和PlaybackControlView。
    
    3.重要的类
    /**
     * 监听播放状态
     */
    private final class ComponentListener implements ExoPlayer.EventListener {
    
        @Override
        public void onPlayerStateChanged(boolean playWhenReady, int playbackState) {
        
            switch (playbackState) {
                /**
                 * The player is able to immediately 
                 /*play from its current position. The player will be playing if
                 * {@link #getPlayWhenReady()} is true, and paused otherwise.
                 */
                 
                case Player.STATE_READY://
                    showLoading(false);
                    play(playWhenReady);
                    break;
                /**
                 * The player has finished playing the media.
                 */
                case Player.STATE_ENDED:
                    showLoading(false);
                    playDefault();
                    break;
                /**
                 * The player does not have any media to play.
                 */
                case Player.STATE_IDLE:
                    showLoading(true);
                    break;
                /**
                 * The player is not able to 
                 /*immediately play from its current position. This state typically
                 * occurs when more data needs to be loaded.
                 */
                case Player.STATE_BUFFERING:
                    showLoading(true);
                    break;
            }
        }


        @Override
        public void onRepeatModeChanged(int repeatMode) {
            // Do nothing.
        }

        @Override
        public void onPositionDiscontinuity() {
            // Do nothing.
        }

        @Override
        public void onPlaybackParametersChanged(PlaybackParameters playbackParameters) {
            // Do nothing.
        }

        @Override
        public void onTimelineChanged(Timeline timeline, Object manifest) {
            // Do nothing.
        }

        @Override
        public void onTracksChanged(TrackGroupArray trackGroups, TrackSelectionArray trackSelections) {
            // Do nothing.
        }

        @Override
        public void onLoadingChanged(boolean isLoading) {
            // Do nothing.
        }

        @Override
        public void onPlayerError(ExoPlaybackException error) {
            // Do nothing.
        }


    }
    4.重要的方法：
    /**
     * 根据不同的Uri文件，生成不同的MediaSource
     *
     * @param uri
     * @param overrideExtension
     * @return
     */
    private MediaSource buildMediaSource(Uri uri, String overrideExtension) {
        int type = Util.inferContentType(!TextUtils.isEmpty(overrideExtension) ? 
        "." + overrideExtension
                : uri.getLastPathSegment());
        switch (type) {
            case C.TYPE_SS:
                return new SsMediaSource(uri, buildDataSourceFactory(false),
                        new DefaultSsChunkSource.Factory(mediaDataSourceFactory), null, null);
            case C.TYPE_DASH:
                return new DashMediaSource(uri, buildDataSourceFactory(false),
                        new DefaultDashChunkSource.Factory(mediaDataSourceFactory), null, null);
            case C.TYPE_HLS:
                return new HlsMediaSource(uri, mediaDataSourceFactory, null, null);
            case C.TYPE_OTHER:
                return new ExtractorMediaSource(uri, mediaDataSourceFactory, new DefaultExtractorsFactory(),
                        null, null);
            default: {
                throw new IllegalStateException("Unsupported type: " + type);
            }
        }
    }
    //

    private DataSource.Factory buildDataSourceFactory(boolean useBandwidthMeter) {
        return buildDataSourceFactory(useBandwidthMeter ? BANDWIDTH_METER : null);
    }


    DataSource.Factory buildDataSourceFactory(DefaultBandwidthMeter bandwidthMeter) {
        return new DefaultDataSourceFactory(getApplicationContext(), bandwidthMeter,
                buildHttpDataSourceFactory(bandwidthMeter));
    }

    HttpDataSource.Factory buildHttpDataSourceFactory(DefaultBandwidthMeter bandwidthMeter) {
        return new DefaultHttpDataSourceFactory(Util.getUserAgent(getApplicationContext(),
        "ExoVideoView"), bandwidthMeter);
    }

