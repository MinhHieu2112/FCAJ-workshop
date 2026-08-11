---
title: "Định vị & bản đồ với Amazon Location Service"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### Tích hợp Amazon Location Service

**Hệ thống quản lý bất động sản cho thuê** cho phép chủ nhà nhập địa chỉ dạng văn bản (ví dụ: *"Số 2 đường Hải Triều, phường Sài Gòn, TP. Hồ Chí Minh"*) và tự động chuyển đổi thành tọa độ địa lý `(Vĩ độ, Kinh độ)` để lưu trữ và cắm ghim trên bản đồ.

#### 1. Tạo API Key trên bảng điều khiển AWS

1. Truy cập [Bảng điều khiển Amazon Location Service](https://console.aws.amazon.com/location/home).
2. Chọn **Create API key**.

![Create API Key Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.1-create-api-key.png)

3. Nhập **Name** (ví dụ: `RealEstateSaaSMapKey`).
4. Tại mục **Resources and actions**, chọn các quyền (actions) gán cho API Key tương ứng với từng tài nguyên:
   * **Maps Actions:** Tích chọn các hành động render bản đồ (ví dụ: `GetStaticMap`, `GetTile`).
   * **Places Actions:** Tích chọn các hành động tra cứu địa điểm/geocoding (ví dụ: `Autocomplete`, `Geocode`, `GetPlace`, `ReverseGeocode`, `SearchNearby`, `SearchText`, `Suggest`).
   * **Routes Actions:** Tích chọn các hành động tính toán tuyến đường nếu ứng dụng có sử dụng (ví dụ: `CalculateRoutes`).

![Create API Key Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.2-set-up.png)

5. *(Tùy chọn)* Tại mục **Expiration time - optional**, chọn ngày (**Date**) và giờ (**Time**) nếu muốn thiết lập thời hạn tự động vô hiệu hóa khóa API Key.
6. *(Tùy chọn)* Tại mục **Client restrictions - optional**, nhấn **Add client** để giới hạn chỉ cho phép các Domain hoặc Referrer chỉ định (ví dụ: `https://yourdomain.com`) được phép sử dụng API Key này.
7. *(Tùy chọn)* Tại mục **Tags - optional**, nhấn **Add new tag** để thêm các thẻ quản lý tài nguyên.
8. Nhấn nút **Create API key** ở góc dưới bên phải để hoàn tất quá trình khởi tạo.

![Create API Key Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.3-save.png)

---

#### 2. Cài đặt SDK Amazon Location Client

Tại thư mục `apps/server`, cài đặt `@aws-sdk/client-location`:

```bash
pnpm add @aws-sdk/client-location
```

---

#### 3. Xây dựng Location Service trong NestJS (`src/location/location.service.ts`)

```typescript
import { Injectable } from '@nestjs/common';
import { LocationClient, SearchPlaceIndexForTextCommand } from '@aws-sdk/client-location';

@Injectable()
export class LocationService {
  private client = new LocationClient({ region: process.env.AWS_REGION || 'us-east-1' });

  async geocodeAddress(addressText: string): Promise<{ latitude: number; longitude: number } | null> {
    const command = new SearchPlaceIndexForTextCommand({
      IndexName: process.env.LOCATION_PLACE_INDEX_NAME || 'RentalPlaceIndex',
      Text: addressText,
      MaxResults: 1,
    });

    const response = await this.client.send(command);
    if (!response.Results || response.Results.length === 0) {
      return null;
    }

    const [longitude, latitude] = response.Results[0].Place.Geometry?.Point || [0, 0];
    return { latitude, longitude };
  }
}
```

---

#### 4. Lưu dữ liệu tọa độ vào PostgreSQL + PostGIS

Khi lưu bất động sản qua Prisma, tọa độ được định dạng dạng `Point(Kinh_độ Vĩ_độ)` cho phép PostGIS truy vấn theo bán kính:

```typescript
// Chèn thông tin bất động sản kèm điểm tọa độ PostGIS
const result = await this.prisma.$executeRaw`
  INSERT INTO "Property" (id, title, address, location)
  VALUES (
    gen_random_uuid(),
    ${dto.title},
    ${dto.address},
    ST_SetSRID(ST_MakePoint(${longitude}, ${latitude}), 4326)
  );
`;
```

#### 5. Kết quả

![Map Search with Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.4-demo.png)
