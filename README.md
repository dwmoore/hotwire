# Toy app to play with Hotwire

## Notes
1. There are two main config options for setting up Hotwire: Using the asset
   pipeline and using webpacker.
2. At the Model layer, we need to configure `broadcast_*` calls. These were
   handled via callback in the tutorial but I'm sure there is a more robust
   option for this architecture.
3. At the View layer, we make use of two primary method calls:
   - `turbo_frame_tag`
   - `turbo_stream_from`
4. `turbo_frame_tag` creates a "slot/frame" in the view for population based on
   turbo stream updates. I believe (and seems reasonable that) all usage of
   `turbo_frame_tag` for the specified resource passed to `turbo_stream_from`
   must be in its downstream render path. A new `turbo_stream_from` will most
   likely need to be defined for any resource used in a template/partial render
   path that intends to call `turbo_frame_tag` with said resource.
   (see https://rubydoc.org/github/hotwired/turbo-rails/main/Turbo/FramesHelper)
5. `turbo_stream_from` creates a subscription to a stream.
    (see https://rubydoc.org/github/hotwired/turbo-rails/main/Turbo%2FStreamsHelper:turbo_stream_from)
6. At the Controller layer, our main change is in the `else` condition of the
   object's `save` call. Here we add a call to `format.turbo_stream` to handle
   the failure scenario. Example:
   ```
   format.turbo_stream {
     render turbo_stream: turbo_stream.replace(
       @tweet,
       partial: "tweets/form",
       locals: { tweet: @tweet }i
     )
   }
   ```
7. The updates around the application layout are not totally clear and may vary
   based on the base configuration option selected (pipeline or webpacker).
   In this repo's case, webpacker is in use resulting in the addition of the
   `turbo_include_tags` in the layout head.
8. Another head tag that should be included is `stimulus_include_tags` but the
   current implementation throws an exception with this line is present.

## Questions
1. What is the correct/optimal configuration for simplified development,
   pipeline or webpacker? (assuming webpacker)
2. What is the correct config for the application layout tags for each setup
   configuration? What other changes are required in each setup scenario?
3. Having not looked at the ViewComponents library yet, how will it factor into
   all the above?
