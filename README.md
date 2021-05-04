// https://www.rfc-editor.org/rfc/rfc4648.txt
const ALPHABET = 'abcdefghijklmnopqrstuvwxyz234567';

export function encode(data: Uint8Array): string {
  let bits = 0;
  let cur = 0;
  let output = '';
  for (let i = 0; i < data.length; i++) {
    cur = (cur << 8) | data[i];
    bits += 8;
    while (bits >= 5) {
      output += ALPHABET[(cur >>> (bits - 5)) & 31];
      bits -= 5;
    }
  }
  if (bits > 0) output += ALPHABET[(cur << (5 - bits)) & 31];
  return output;
}

export function decode(input: string): Uint8Array {
  let bits = 0;
  let cur = 0;
  let idx = 0;
  const output = new Uint8Array(((input.length * 5) / 8) | 0);
  for (let i = 0; i < input.length; i++) {
    cur = (cur << 5) | ALPHABET.indexOf(input[i]);
    bits += 5;
    if (bits < 8) continue;
    output[idx++] = (cur >>> (bits - 8)) & 255;
    bits -= 8;
  }
  return output;
}
