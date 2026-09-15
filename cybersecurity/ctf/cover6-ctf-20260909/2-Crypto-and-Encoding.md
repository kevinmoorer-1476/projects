
## Rotten

P6F{ebggra_ohg_abg_sbetbggra}

That's a flag. It's just not in the right order.

**Solution:**\
"ROT" is a clue. From Google: *"A **ROT cipher** (short for **rotate cipher**) is a simple letter substitution method that replaces each letter with another letter located a fixed number of positions further down the alphabet"* 

Found this site: https://www.dcode.fr/rot-cipher

It decoded in multiple ways and one possible solution revealed the flag (ROT rotation -13)

## Base Camp

Somebody encoded this. Then encoded it again. Then, for reasons known only to them, once more. Peel it.

**Solution:**\
file: 08-base-camp.txt

Value has == at end which suggested base64 encoding. Was told this was encoded 3 times.

Tried base64 and after the first pass it looked ok:
`$ echo S0Y1RlVWREZHSjRHUVpLWEtaNFdHTUpaTkJSVzJWVEdNSldUU01DWUdKTEhLV0pUSkkyV0dTQ1NPQlJERU5KWg== | base64 -d.\
`$ KF5FUVDFGJ4GQZKXKZ4WGMJZNBRW2VTGMJWTSMCYGJLHKWJTJI2WGSCSOBRDENJZ`

but on the second pass it was gibberish. Had to find another decoder. 

Found [https://www.dcode.fr/cipher-identifier](https://www.dcode.fr/cipher-identifier) and tried base32 as it was their top suggestion. Used their online tool at https://www.dcode.fr/base-32-encoding and got: QzZTe2xheWVyc19hcmVfbm90X2VuY3J5cHRpb259

I could have also used:\
`python3 -c "import base64; print(base64.b32decode('KF5FUVDFGJ4GQZKXKZ4WGMJZNBRW2VTGMJWTSMCYGJLHKWJTJI2WGSCSOBRDENJZ').decode('utf-8'))"`

Tried base32 as third pass and got trash. Tried base64 again and got the flag.
