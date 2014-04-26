P6-Compress-Zlib
================

## Name ##

Compress::Zlib - A (hopefully) nice interface to zlib

## Description ##

Compresses and uncompresses data using zlib.

## Example Usage ##

    use Compress::Zlib;
    
    my $compressed = compress($string.encode('utf8'));
    my $original = uncompress($compressed).decode('utf8');


    my $compressor = Compress::Zlib::Stream.new;
    loop {
        $socket.write($compressor.deflate($data-chunk));
    }
    $socket.write($compressor.finish);

    my $decompressor = Compress::Zlib::Stream.new;
    while !$decompressor.finished {
        my $data-chunk = $decompressor.inflate($socket.read($size));
    }

## Functions ##

 -  `compress(Blob $data, Int $level? --> Buf)`

    Compresses binary $data. $level is the optional compression level (0 <= x <= 9); defaults to 6.

 -  `uncompress(Blob $data --> Buf)`

    Uncompresses previously compressed $data.

## Stream Class ##

This currently has very few options. Over time, I will add support for custom
compression levels, gzip/raw deflate streams, etc. If you need a specific feature,
open an issue and I will move it to the top of my priority list.

 -  `new( --> Compress::Zlib::Stream)`

    Creates a new object that can be used for either compressing or decompressing
    (but not both!).

 -  `finished( --> Bool)`

    Returns true if the end of the data stream has been reached. In this case,
    you can do nothing more that's useful with this object.

 -  `inflate(Buf $data --> Buf)`

    Decompresses a chunk of data.

 -  `deflate(Buf $data --> Buf)`

    Compresses a chunk of data.

 -  `flush( --> Buf)`

    Currently just returns an empty Buf, as inflate and deflate are using Z_SYNC_FLUSH.
    To be future-proof, call this any time you need Z_SYNC_FLUSH semantics (sending
    a complete request to a server, for example).

    At some point, will cause zlib to flush some of it's internal data structures
    and possibly return more data.

 -  `finish( --> Buf)`

    Flushes all remaining data from zlib's internal structures, and deallocates
    them.

    Call when you want a Z_STREAM_END to happen when compressing, or if you are
    finished with the object.
