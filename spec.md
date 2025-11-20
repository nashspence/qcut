# qcut

## Scenario: assemble highlight reel from a directory
* Given a directory "<src>" containing source videos
* And an output directory "<out>"
* When I set environment variable "QCUT_SRC_DIR" to "<src>"
* And I set environment variable "QCUT_OUT_DIR" to "<out>"
* And I set environment variable "QCUT_TARGET_SECONDS" to "<seconds>"
* And I set environment variable "QCUT_MIN_SECONDS" to "<min>"
* And I set environment variable "QCUT_MAX_SECONDS" to "<max>"
* And I set environment variable "QCUT_SVT_PRESET" to "<preset>"
* And I set environment variable "QCUT_SVT_CRF" to "<crf>"
* And I set environment variable "QCUT_SVT_LP" to "<lp>"
* And I set environment variable "QCUT_OPUS_BR" to "<bitrate>"
* And I set environment variable "QCUT_FONT_FILE" to "<font>"
* And I run qcut
* Then qcut encodes Matroska clips into "<out>"
* And qcut appends the clips with mkvmerge
* And ".job.json" in "<out>" records each clip
* And the final output uses the SVT-AV1 lp "<lp>"

## Scenario: skip already completed highlight reel
* Given a directory "<src>" containing source videos
* And an output directory "<out>"
* And a manifest in "<out>" whose "final" status is "done"
* And the manifest references a finished video in "<out>"
* When I set environment variable "QCUT_SRC_DIR" to "<src>"
* And I set environment variable "QCUT_OUT_DIR" to "<out>"
* And I run qcut
* Then qcut prints the existing output file name
* And qcut exits without re-encoding clips

## Scenario: resume probes and clip encodes
* Given a directory "<src>" containing source videos
* And an output directory "<out>"
* And a manifest in "<out>" with partial probe results
* When I set environment variable "QCUT_SRC_DIR" to "<src>"
* And I set environment variable "QCUT_OUT_DIR" to "<out>"
* And I run qcut
* Then qcut reuses probe durations from the manifest
* And qcut continues encoding remaining clips
* And the manifest records updated timestamps

## Scenario: enable verbose debug logging
* Given a directory "<src>" containing source videos
* And an output directory "<out>"
* When I set environment variable "QCUT_SRC_DIR" to "<src>"
* And I set environment variable "QCUT_OUT_DIR" to "<out>"
* And I set environment variable "QCUT_DEBUG_CMDS" to "1"
* And I set environment variable "QCUT_VERBOSE" to "1"
* And I run qcut
* Then qcut prints ffmpeg and ffprobe commands
* And qcut prints mkvmerge commands
