---
title: "Geocoding & mapping with Amazon Location Service"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Amazon Location Service integration

The **Real Estate Rental Management System** enables property managers to input textual addresses (e.g., *"2 Hai Trieu Street, Saigon Ward, District 1, Ho Chi Minh City"*) and automatically converts them into geographical `(Latitude, Longitude)` coordinates for spatial storage and map rendering.

#### 1. Create an API Key in AWS Console

1. Open the [Amazon Location Service Console](https://console.aws.amazon.com/location/home).
2. Select **Create API key**.
![Create API Key](images/5-Workshop/5.5-Location-Service/5.5.1-create-api-key.png)

3. Enter **Name** (e.g., `RealEstateSaaSMapKey`).
4. Under **Resources and actions**, select permissions assigned to the API Key:
   - **Maps Actions:** Select tile and static map render permissions (`GetStaticMap`, `GetTile`).
   - **Places Actions:** Select geocoding and search actions (`Autocomplete`, `Geocode`, `GetPlace`, `ReverseGeocode`, `SearchNearby`, `SearchText`, `Suggest`).
   - **Routes Actions:** Select routing actions if needed (`CalculateRoutes`).
![Set up API Key](/images/5-Workshop/5.5-Location-Service/5.5.2-set-up.png)

5. *(Optional)* Under **Client restrictions**, restrict usage to specified application domains (e.g., `https://yourdomain.com`).
6. Click **Create API key** to complete setup.
![Save API Key](/images/5-Workshop/5.5-Location-Service/5.5.3-save.png)

---

#### 2. Install Amazon Location Client SDK

In your NestJS backend directory (`apps/server`), install `@aws-sdk/client-location`:

```bash
pnpm add @aws-sdk/client-location
```

---

#### 3. Implement Location Service in NestJS (`src/location/location.service.ts`)

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

#### 4. Store spatial coordinates in PostgreSQL + PostGIS

When saving property listings via Prisma, coordinates are stored using the `Point(Longitude Latitude)` format, enabling PostGIS radius queries:

```typescript
// Insert property record with PostGIS spatial point
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

#### 5. Interactive map search interface

![Map Search Demo](/images/5-Workshop/5.5-Location-Service/5.5.4-demo.png)
