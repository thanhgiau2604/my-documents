---
sidebar_position: 1
---

## CÁCH DEPLOY REACT APP LÊN SURGE.SH

- `npm i -g surge` --> install global `surge`

- `npm run build` --> build mode `production`

- `cd build`

- `surge`

- Nếu lần đầu deploy sẽ yêu cầu nhập `email/password`

- Sau đó nhập `tên domain` --> `Enter`

- Khi deploy thành công. Chúng ta thấy website chỉ hoạt động ở path root "/", còn ở các path khác lại trả về lỗi 404 --> Do surge chỉ tìm thấy file index.html cho path root, còn các ở path khác không tim thấy file tương ứng.

--> **Solution**: Thêm 1 file 200.html (copy từ file index.html), surge sẽ tự động tìm file này thay thế

**Deploy auto with script**

Example script: (example filename: deploy-surge.sh)

```js
# Build reactjs app with production mode
npm run build

# Move to build folder
cd build

# Clone index.html into 200.html
cp index.html 200.html

# Start deploying via Surge
# The command means deploy current folder to domain paul-photo-app.surge.sh
surge . <domain name>.surge.sh
```

Trong file package.json add script để deploy:
`"deploy": "sh ./deploy-surge.sh"`

--> **npm run deploy**

