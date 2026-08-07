---
title: "Address Geocoding with Amazon Location Service"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Amazon Location Service Integration

The **real estate rental management system** enables landlords to enter human-readable addresses (e.g., *"2 Hai Trieu Street, Ben Nghe, District 1, HCMC"*) and automatically converts them into geographic coordinates `(Latitude, Longitude)` for spatial storage and map pin rendering.

#### 1. Create an API Key via AWS Console

1. Open the [Amazon Location Service Console](https://console.aws.amazon.com/location/home).
2. Select **Create API key**.

![Create API Key Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.1-create-api-key.png)

3. Enter **Name** (e.g., `RealEstateSaaSMapKey`).
4. Under **Resources and actions**, select actions assigned to the API Key corresponding to each resource:
   * **Maps Actions:** Check map rendering actions (e.g., `GetStaticMap`, `GetTile`).
   * **Places Actions:** Check place lookup/geocoding actions (e.g., `Autocomplete`, `Geocode`, `GetPlace`, `ReverseGeocode`, `SearchNearby`, `SearchText`, `Suggest`).
   * **Routes Actions:** Check route calculation actions if used by the application (e.g., `CalculateRoutes`).

![Create API Key Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.2-set-up.png)

5. *(Optional)* Under **Expiration time - optional**, select Date and Time if you want to set an automatic expiration period for the API Key.
6. *(Optional)* Under **Client restrictions - optional**, click **Add client** to restrict allowed Domains or Referrers (e.g., `https://yourdomain.com`).
7. *(Optional)* Under **Tags - optional**, click **Add new tag** to add resource management tags.
8. Click **Create API key** button at the bottom right to complete initialization.

![Create API Key Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.3-save.png)

---

#### 2. Install AWS Location Client SDK

In `apps/server`, install `@aws-sdk/client-location`:

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

#### 4. Storing Spatial Data in PostgreSQL + PostGIS

When saving properties in Prisma, coordinates are formatted as `Point(Longitude Latitude)` for PostGIS queries:

```typescript
// Insert property with PostGIS spatial point
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

#### 5. Results

![Map Search with Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.4-demo.png)
