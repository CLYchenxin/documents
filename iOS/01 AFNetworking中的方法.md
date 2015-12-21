#AFNetworking中的方法
###1、URLEncode

``` obecect-c
static NSString * AFPercentEscapedStringFromString(NSString *string) {
	static NSString * const kAFCharactersGeneralDelimitersToEncode = @":#[]@"; // does not include "?" or "/" due to RFC 3986 - Section 3.4
	static NSString * const kAFCharactersSubDelimitersToEncode = @"!$&'()*+,;=";

	NSMutableCharacterSet * allowedCharacterSet = [[NSCharacterSet URLQueryAllowedCharacterSet] mutableCopy];
	[allowedCharacterSet removeCharactersInString:[kAFCharactersGeneralDelimitersToEncode stringByAppendingString:kAFCharactersSubDelimitersToEncode]];

	// FIXME: https://github.com/AFNetworking/AFNetworking/pull/3028
	// return [string stringByAddingPercentEncodingWithAllowedCharacters:allowedCharacterSet];

	static NSUInteger const batchSize = 50;

	NSUInteger index = 0;
	NSMutableString *escaped = @"".mutableCopy;

	while (index < string.length) {
#pragma GCC diagnostic push
#pragma GCC diagnostic ignored "-Wgnu"
		NSUInteger length = MIN(string.length - index, batchSize);
#pragma GCC diagnostic pop
		NSRange range = NSMakeRange(index, length);

        // To avoid breaking up character sequences such as 👴🏻👮🏽
		range = [string rangeOfComposedCharacterSequencesForRange:range];

		NSString *substring = [string substringWithRange:range];
		NSString *encoded = [substring stringByAddingPercentEncodingWithAllowedCharacters:allowedCharacterSet];
		[escaped appendString:encoded];

		index += range.length;
	}

	return escaped;
}
```

###2、Base64编码

``` ocject-c
static NSString * AFBase64EncodedStringFromString(NSString *string) {
    NSData *data = [NSData dataWithBytes:[string UTF8String] length:[string lengthOfBytesUsingEncoding:NSUTF8StringEncoding]];
    NSUInteger length = [data length];
    NSMutableData *mutableData = [NSMutableData dataWithLength:((length + 2) / 3) * 4];

    uint8_t *input = (uint8_t *)[data bytes];
    uint8_t *output = (uint8_t *)[mutableData mutableBytes];

    for (NSUInteger i = 0; i < length; i += 3) {
        NSUInteger value = 0;
        for (NSUInteger j = i; j < (i + 3); j++) {
            value <<= 8;
            if (j < length) {
                value |= (0xFF & input[j]);
            }
        }

        static uint8_t const kAFBase64EncodingTable[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";

        NSUInteger idx = (i / 3) * 4;
        output[idx + 0] = kAFBase64EncodingTable[(value >> 18) & 0x3F];
        output[idx + 1] = kAFBase64EncodingTable[(value >> 12) & 0x3F];
        output[idx + 2] = (i + 1) < length ? kAFBase64EncodingTable[(value >> 6)  & 0x3F] : '=';
        output[idx + 3] = (i + 2) < length ? kAFBase64EncodingTable[(value >> 0)  & 0x3F] : '=';
    }

    return [[NSString alloc] initWithData:mutableData encoding:NSASCIIStringEncoding];
}
```

