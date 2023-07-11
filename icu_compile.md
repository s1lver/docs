# Сборка ICU

```bash
wget https://github.com/unicode-org/icu/releases/download/release-65-1/icu4c-65_1-src.tgz
```

```bash
tar -zxvf icu4c-65_1-src.tgz
```

```bash
cd ./icu/source
```

```bash
./configure --prefix=/opt/icu
```

```bash
sudo make && make install
```
