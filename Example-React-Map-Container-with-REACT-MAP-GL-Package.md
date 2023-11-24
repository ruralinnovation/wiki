# React Map Container Leveraging `react-map-gl` Package

```Typescript
import { ApolloProvider, useQuery } from '@apollo/client';
import { Box, Container } from '@mui/material';
import { useEffect, useRef, useState } from 'react';
import mapboxgl from 'mapbox-gl';

import {
  broadband_unserved_blocks_geojson,
  county_broadband_farm_bill_eligibility_geojson,
  incumbent_electric_providers_geo_geojson,
} from '../services/bcatQueries';
import Map, { Layer, Source, FillLayer, LineLayer } from 'react-map-gl';

// For more information on data-driven styles, see https://www.mapbox.com/help/gl-dds-ref/
export const dataLayer1: LineLayer = {
  id: 'county_broadband_farm_bill_eligibility_line_layer',
  source: 'test',
  type: 'line',
  paint: {
    'line-color': 'black',
  },
};
export const dataLayer2: FillLayer = {
  id: 'county_broadband_farm_bill_eligibility_fill_layer',
  source: 'test',
  type: 'fill',
  paint: {
    'fill-color': '#0080ff', // blue color fill
    'fill-opacity': 0.5,
  },
};

function MapContainer() {
  
  const [viewport, setViewport] = useState();
  
  const { loading, error, data } = useQuery(county_broadband_farm_bill_eligibility_geojson, {
    fetchPolicy: 'no-cache',
    variables: {
      state_abbr: 'TN',
      skipCache: true,
    },
  });

  useEffect(() => {
    console.log('ERROR ', error);
  }, [error]);

  if (loading) return <div>Loading Data</div>;


  return (
    <Container maxWidth="xl">
      <Box sx={{ bgcolor: '#cfe8fc', height: '100vh' }}>
        {data && (
          <Map
            initialViewState={{
              longitude: -86.503,
              latitude: 35.562,
              zoom: 7,
            }}
            style={{ width: '100%', height: '100vh' }}
            mapStyle="mapbox://styles/mergingfutures/ckyn2t9jv0una14prs29fkgy2">
            <Source type="geojson" id="test" data={data.county_broadband_farm_bill_eligibility_geojson}>
              <Layer {...dataLayer1} />
              <Layer {...dataLayer2} />
            </Source>
          </Map>
        )}
      </Box>
    </Container>
  );
}

export default MapContainer;

```

# Example GraphQL Query File for Apollo-Client

> Note:  These queries are ALL available in the Postman Environment.

```Typescript
import { gql } from '@apollo/client';

export const broadband_unserved_blocks_geojson = gql`
  query ($counties: [String], $skipCache: Boolean) {
    broadband_unserved_blocks_geojson(counties: $counties, skipCache: $skipCache) {
      type
      features {
        type
        id
        properties
        geometry {
          coordinates
          type
        }
      }
    }
  }
`;

export const broadband_unserved_blocks_county_geojson = gql`
  query ($county: String!, $skipCache: Boolean) {
    broadband_unserved_blocks_county_geojson(county: $county, skipCache: $skipCache) {
      type
      features {
        type
        id
        properties
        geometry {
          coordinates
          type
        }
      }
    }
  }
`;

export const county_broadband_farm_bill_eligibility_geojson = gql`
  query ($state_abbr: String!, $skipCache: Boolean) {
    county_broadband_farm_bill_eligibility_geojson(state_abbr: $state_abbr, skipCache: $skipCache) {
      type
      features {
        type
        id
        properties
        geometry {
          coordinates
          type
        }
      }
    }
  }
`;

export const incumbent_electric_providers_geo_geojson = gql`
  query ($state_abbr: String!, $skipCache: Boolean) {
    incumbent_electric_providers_geo_geojson(state_abbr: $state_abbr, skipCache: $skipCache) {
      type
      features {
        type
        id
        properties
        geometry {
          coordinates
          type
        }
      }
    }
  }
`;

```
