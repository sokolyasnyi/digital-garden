**Forwarded from [Stanislav Sokolov](https://t.me/sokol_yasnyi)**

# error block is executed when a error occurs
error do |lane, exception|
  slack(
    # message with short human friendly message
    message: exception.to_s,
    success: false,
    # Output containing extended log output
    payload: { "Output" => exception.error_info.to_s }
  )
end