# androidtvremote2

A Python library for interacting with Android TV using the Android TV Remote protocol v2. This is the same protocol the Google TV mobile app is using. It doesn't require ADB or enabling developer tools on the Android TV device. It only requires the [Android TV Remote Service](https://play.google.com/store/apps/details?id=com.google.android.tv.remote.service) that comes pre-installed on most Android TV devices.

For a list of the most common commands you can send to the Android TV see: [TvKeys](https://github.com/tronikos/androidtvremote2/blob/main/TvKeys.txt).
For a full list see [here](https://github.com/tronikos/androidtvremote2/blob/main/src/androidtvremote2/remotemessage.proto#L90).
In addition to commands you can send URLs to open apps registered to handle them.

## Credits

- Official [implementation](https://android.googlesource.com/platform/external/google-tv-pairing-protocol/+/refs/heads/master) of the pairing protocol in Java
- [Implementation](https://github.com/farshid616/Android-TV-Remote-Controller-Python) in Python but for the old v1 protocol
- [Implementation](https://github.com/louis49/androidtv-remote) in Node JS for the v2 protocol
- [Description](https://github.com/Aymkdn/assistant-freebox-cloud/wiki/Google-TV-(aka-Android-TV)-Remote-Control-(v2)) of the v2 protocol

## Example

See [demo.py](https://github.com/tronikos/androidtvremote2/blob/main/src/demo.py)

If you get an error: `ssl.SSLError: [SSL: SSLV3_ALERT_HANDSHAKE_FAILURE] sslv3 alert handshake failure (_ssl.c:1131)`
then you need to find the android devices certificate name and mac manually by running:

```
openssl s_client -connect <ANDROID TV IP>:6467 -prexit -state 2>/dev/null | grep "issuer="
```

You are looking for details of the certificate a few examples below.

Your MAC is the bit after the last `/` and the device name is the bit between the '/' before the MAC.

Example line to look for on a Humax Aura box:
```
issuer=CN = atvremote/fvp4kgtr/fvp4kgtr/FVP-4KGTR/AB:CD:12:34:56:78
```
So in this example mac is `AB:CD:12:34:56:78` and the device name is `FVP-4KGTR`


Example from Sony Bravia TV:
```
issuer=CN = atvremote/BRAVIA_ATV2_EU/BRAVIA_ATV2/BRAVIA 4K GB/AB:CD:12:34:56:78
```
So in this example mac is `AB:CD:12:34:56:78` and the device name is `BRAVIA 4K GB`


Example from a Philips TV:
```
issuer=dnQualifier = PH9M_EA_5599/PH9M_EA_5599/TPM191E, CN = atvremote/44:D8:78:89:08:62
issuer=dnQualifier = PH9M_EA_5599/PH9M_EA_5599/TPM191E, CN = atvremote/44:D8:78:89:08:62
```

So in this example mac is `AB:CD:12:34:56:78` and the device name is `atvremote`

Pass these as arguments to demo:
```
python demo.py --server-name "FVP-4KGTR" --server-mac "AB:CD:12:34:56:78"
```

## Development environment

```sh
python3 -m venv .venv
source .venv/bin/activate
# for Windows CMD:
# .venv\Scripts\activate.bat
# for Windows PowerShell:
# .venv\Scripts\Activate.ps1

# Install dependencies
python -m pip install --upgrade pip
python -m pip install .

# Generate *_pb2.py from *.proto
python -m pip install grpcio-tools
python -m grpc_tools.protoc src/androidtvremote2/*.proto --python_out=src/androidtvremote2 -Isrc/androidtvremote2

# Run formatter
python -m pip install isort black
isort .
black .

# Run lint
python -m pip install flake8 ruff
flake8 .
ruff .

# Run tests
python -m pip install pytest
pytest

# Run demo
python -m pip install pynput zeroconf
python src/demo.py

# Build package
python -m pip install build
python -m build
```
