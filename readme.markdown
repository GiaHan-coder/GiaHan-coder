import tkinter as tk
from tkinter import ttk
import requests
from PIL import Image, ImageTk
import io
import webbrowser
import os

# Thiết lập cửa sổ
WIDTH, HEIGHT = 800, 600
root = tk.Tk()
root.title("Interactive Image Gallery")
canvas = tk.Canvas(root, width=WIDTH, height=HEIGHT, bg="#000000")
canvas.pack()

# Danh sách hình ảnh và liên kết (trích từ HTML của bạn)
image_data = [
    {"light": "https://leonsilicon.github.io/leonsilicon/generator/generated/42d246e86dbd4a939d9ccc6591219bcc57d6183956a6246c35d1be6df80379de6dc778b625a5ca42a5fe7e3c2e8becdc57956223d8f0c57541d751058e937369.png",
     "dark": "https://leonsilicon.github.io/leonsilicon/generator/generated/6b167f71a782362752b814be67a46b262f0c4ee2dce9500fd24df9311bee952feb206b2402e964e769e77a5884dbb69f9ca52ec778fbc3e705c48cd57eb9dfa8.png",
     "width": 2.4822695035460995, "link": None},
    {"light": "https://leonsilicon.github.io/leonsilicon/generator/generated/d3d8559acdac9fc85ced576830bafd7b6054c216a3617652f365cbe1a91e5262156d8fbab4839ed3115d4887f244c883cce95b9b506e0ea5529044244658c8aa.png",
     "dark": "https://leonsilicon.github.io/leonsilicon/generator/generated/0e4cc93fce5aa568da6ce76830853d9cbbc0993c56bb92d5e7bf1bdad06ff401c0b097dd58a67835a23ac0b3cd9b2503fa76ad4206b83212c3768a238467deb3.png",
     "width": 1.4184397163120568, "link": "#js-contribution-activity"},
    {"light": "https://leonsilicon.github.io/leonsilicon/generator/generated/becc65165b902b95c14ae12bb2eb936e39ae7ad5a983b2c852ad0e49b3e64a09a7da09590d94cb0a40269fea2b2a5caa90ddae87f20719ef25d4b2a5d16c3f81.png",
     "dark": "https://leonsilicon.github.io/leonsilicon/generator/generated/7fbbe36fef3ff0896a30964782c7d9836c84e98d5e6ec86be12ca676c252addaeedc1a2d15f06bf6f428959c691a timing500fd7b2ef287777934d4c33d628ebbb1c00.png",
     "width": 0.9456264775413712, "link": None},
    {"light": "https://leonsilicon.github.io/leonsilicon/generator/generated/1b0c29f41cdd662baacb6a760d01fede85ce0ca131e57560d7313ddb78effadf3fd11d32b28df184006d90cc3cbe29e1021de694d0698ebca5395792e0c6859a.png",
     "dark": "https://leonsilicon.github.io/leonsilicon/generator/generated/1b4138c079e1db237af31e6cfd1ad0b242398827624defff86a1b7a89fdd556cd817e199df9ae081f8f8fa134788cd7edf136665487aa49e4c2d897d7da791f2.png",
     "width": 1.4184397163120568, "link": "#-the-above-image-is-interactive-try-clicking-on-the-tabs-"},
    # Thêm các hình ảnh khác nếu muốn (danh sách dài, tôi chỉ lấy vài ví dụ)
    {"light": "https://leonsilicon.github.io/leonsilicon/generator/generated/c018dd35c73a7ccf815cf5d3ec1d4ce842149c3773dbc4ad3547f7cc36871fa0b0b5902f9b84feff3c82447a42424b4fa5caf2e55add999627d39326f55812a9.png",
     "dark": "https://leonsilicon.github.io/leonsilicon/generator/generated/e2ad626bf8fd369bb48b4afa352ad9055d275461ac07e2d49452dc1ab14f5615c8e75dc188542758f7cd33577bcbb96e7fcf53526730f0205728e133b2bab396.png",
     "width": 14.420803782505912, "link": "https://leonsilicon.com"},
    {"light": "https://leonsilicon.github.io/leonsilicon/generator/generated/70fe32567f4c1575937f35e9f0ed850ecab161eb2b5e6267541b6b7042c3ee08710ad3c1b9dcc4b5d533979dc07dc48f886c3fae327e34d072c55c26b436809c.png",
     "dark": "https://leonsilicon.github.io/leonsilicon/generator/generated/ba54ffe367904eaad340121ae3dcbd0676dd125fad97c80372ffb097e58d34d2cc1149d8bfb0a3df9db539f31356dbeb602265b6c749b564c3e1169ca83c367f.png",
     "width": 14.893617021276595, "link": "https://instagram.com/leonsilicon"},
]

# Hàm tải hình ảnh từ URL
def load_image(url):
    response = requests.get(url)
    img_data = response.content
    img = Image.open(io.BytesIO(img_data))
    return img

# Hàm xử lý nhấp chuột
def on_image_click(event, link):
    if link and link.startswith("http"):
        webbrowser.open(link)
    elif link and link.startswith("#"):
        print(f"Clicked anchor: {link}")  # Xử lý anchor trong ứng dụng nếu cần

# Danh sách để lưu trữ tham chiếu hình ảnh Tkinter
photo_images = []

# Hiển thị hình ảnh
x_pos = 0
for data in image_data:
    # Chọn hình ảnh (light/dark) - ở đây dùng light làm mặc định
    img = load_image(data["light"])  # Có thể thêm logic kiểm tra theme hệ thống
    width_percent = data["width"]
    new_width = int(WIDTH * width_percent / 100)
    aspect_ratio = img.height / img.width
    new_height = int(new_width * aspect_ratio)
    img = img.resize((new_width, new_height), Image.Resampling.LANCZOS)

    # Chuyển đổi sang định dạng Tkinter
    photo = ImageTk.PhotoImage(img)
    photo_images.append(photo)  # Giữ tham chiếu để tránh bị garbage collected

    # Tạo hình ảnh trên canvas
    img_id = canvas.create_image(x_pos, HEIGHT // 2, image=photo, anchor="w")

    # Thêm sự kiện nhấp chuột nếu có liên kết
    if data["link"]:
        canvas.tag_bind(img_id, "<Button-1>", lambda e, l=data["link"]: on_image_click(e, l))

    # Cập nhật vị trí x cho hình ảnh tiếp theo
    x_pos += new_width

# Chạy ứng dụng
root.mainloop()
