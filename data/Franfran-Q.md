`onMinted()` is called when after token is minted.
`tokenAttributes` is computed from a "seed" that is right shifted 16 times for each property, which means that after 16 iterations (16 x 16 = 256), the seed will always be 0 and so `tokenAttributes`

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L194-L197