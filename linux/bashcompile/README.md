## 1. Install add-ons
```
apt update && apt install gcc shc -y
```

## 2. How to use
```
cat << 'EOF' > hello.sh
echo hello $1
EOF

shc -f hello.sh
```

## 3. Verify
```
cat hello.sh
cat hello.sh.x
bash hello.sh.x test
```

## Reference
- https://www.simplified.guide/bash/compile-script


[Back](../)
