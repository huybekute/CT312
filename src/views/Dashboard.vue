<template>
  <div class="flex h-screen bg-[#f7f9fa] text-slate-800 font-sans antialiased overflow-hidden">

    <!-- SIDEBAR BÊN TRÁI: BỘ LỌC ĐỘNG TỪ EXCEL -->
    <aside class="w-64 bg-[#2d3748] text-slate-300 flex flex-col shadow-xl z-10 select-none">
      <div class="p-5 bg-[#1a202c] border-b border-slate-700 flex items-center gap-3">
      </div>

      <div class="flex-1 overflow-y-auto p-4 space-y-4 custom-scrollbar">
        <!-- 1. Bộ lọc Năm -->
        <div>
          <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-1.5">Năm (year)</label>
          <select v-model="selectedYear"
            class="w-full bg-[#1a202c] border border-slate-700 rounded-lg p-2.5 text-xs font-medium text-slate-300 focus:outline-none focus:ring-1 focus:ring-blue-500 cursor-pointer">
            <option value="">-- Tất cả các năm ({{ years.length }} năm) --</option>
            <option v-for="y in years" :key="y" :value="y">Năm {{ y }}</option>
          </select>
        </div>

        <!-- 2. Bộ lọc Khu vực -->
        <div>
          <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-1.5">Khu vực (region)</label>
          <select v-model="selectedRegion"
            class="w-full bg-[#1a202c] border border-slate-700 rounded-lg p-2.5 text-xs font-medium text-slate-300 focus:outline-none focus:ring-1 focus:ring-blue-500 cursor-pointer">
            <option value="">-- Tất cả khu vực ({{ regions.length }}) --</option>
            <option v-for="r in regions" :key="r" :value="r">Khu vực {{ r }}</option>
          </select>
        </div>

        <!-- 3. Bộ lọc Quốc gia -->
        <div>
          <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-1.5">Quốc gia
            (country)</label>
          <select v-model="selectedCountry"
            class="w-full bg-[#1a202c] border border-slate-700 rounded-lg p-2.5 text-xs font-medium text-slate-300 focus:outline-none focus:ring-1 focus:ring-blue-500 cursor-pointer">
            <option value="">-- Tất cả quốc gia ({{ countries.length }}) --</option>
            <option v-for="c in countries" :key="c" :value="c">{{ c }}</option>
          </select>
        </div>
      </div>
    </aside>

    <!-- KHU VỰC HIỂN THỊ NỘI DUNG CHÍNH (CANVAS) -->
    <main class="flex-1 flex flex-col overflow-y-auto">

      <!-- TOP BANNER BAR -->
      <div
        class="bg-[#2d3748] text-white px-6 py-4 flex flex-col md:flex-row justify-between items-start md:items-center shadow-md border-b border-slate-800">
        <div>
          <h1 class="text-lg font-black tracking-wider uppercase flex items-center gap-2">
            PHÂN TÍCH DỮ LIỆU BỆNH LAO TOÀN CẦU GIAI ĐOẠN 2020 - 2024
          </h1>
        </div>
        <div
          class="text-[11px] font-mono text-slate-400 bg-[#1a202c] px-3 py-1 rounded border border-slate-700 mt-2 md:mt-0">
          Dữ liệu: {{ filteredData.length }} / {{ fullData.length }} dòng
        </div>
      </div>

      <div class="p-6 space-y-6 flex-1 bg-[#f0f2f5]">
        <section class="grid grid-cols-2 md:grid-cols-4 gap-4">
          <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-100 text-center">
            <div class="text-[11px] font-bold text-slate-400 uppercase tracking-tight">Tổng ca mắc</div>
            <div class="text-xl font-black text-[#2b6cb0] mt-1">{{ Number(kpiTotals.incidence).toLocaleString('vi-VN')
              }}</div>
          </div>
          <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-100 text-center">
            <div class="text-[11px] font-bold text-slate-400 uppercase tracking-tight">Tỷ lệ tử vong trung bình</div>
            <div class="text-xl font-black text-[#e53e3e] mt-1">{{ kpiTotals.cfrAvg }}%</div>
          </div>
          <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-100 text-center">
            <div class="text-[11px] font-bold text-slate-400 uppercase tracking-tight">Tỷ lệ mắc trung bình </div>
            <div class="text-xl font-black text-indigo-600 mt-1">{{ kpiTotals.inc100kAvg }} / 100 nghìn dân</div>
          </div>
          <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-100 text-center">
            <div class="text-[11px] font-bold text-slate-400 uppercase tracking-tight">Tổng thâm hụt tài chính</div>
            <div class="text-xl font-black text-amber-600 mt-1">${{
              Number(kpiTotals.financialGap).toLocaleString('vi-VN') }}</div>
          </div>
        </section>
        <!-- <section class="grid grid-cols-1 lg:grid-cols-12 gap-5">
          <div class="lg:col-span-6 bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">BẢN ĐỒ PHÂN BỐ DỊCH TỄ TOÀN CẦU</h3>
            <div class="h-[280px]">
              <v-chart v-if="isMapReady" class="w-full h-full" :option="mapOptions" autocall-resize />
              <div v-else class="h-full flex items-center justify-center text-xs text-slate-400">đang tải</div>
            </div>
          </div>

          <div class="lg:col-span-6 bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Xu hướng tử vong do lao</h3>
            <apexchart type="line" height="280" :options="chart2Options" :series="chart2Series"></apexchart>
          </div>
        </section> -->
        <section class="grid grid-cols-1 gap-5">
          <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">
              BẢN ĐỒ PHÂN BỐ DỊCH TỄ TOÀN CẦU
            </h3>

            <div class="h-[320px]">
              <v-chart v-if="isMapReady" class="w-full h-full" :option="mapOptions" autocall-resize />

              <div v-else class="h-full flex items-center justify-center text-xs text-slate-400">
                đang tải
              </div>
            </div>
          </div>
        </section>

        <!-- HÀNG 3: XU HƯỚNG TỬ VONG NẰM CẠNH TOP 10 -->
        <section class="grid grid-cols-1 lg:grid-cols-2 gap-5">
          <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">
              Xu hướng tử vong do lao
            </h3>

            <apexchart type="line" height="300" :options="chart2Options" :series="chart2Series" />
          </div>

          <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">
              Top 10 quốc gia có tỷ lệ mắc lao cao nhất
            </h3>


            <apexchart type="bar" height="300" :options="top10IncidenceOptions" :series="top10IncidenceSeries" />
          </div>
        </section>
        <section class="grid grid-cols-1 lg:grid-cols-2 gap-5">
          <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Tương quan đồng nhiễm HIV và tỷ
              lệ tử vong</h3>
            <apexchart type="bubble" height="250" :options="chart3Options" :series="chart3Series" />
          </div>

          <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1"> Đánh giá khoảng trống tài chính
              y tế toàn cầu</h3>
            <apexchart type="bar" height="250" :options="chart4Options" :series="chart4Series" />
          </div>
        </section>

        <section class="grid grid-cols-1 lg:grid-cols-12 gap-5">
          <div class="lg:col-span-7 bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Kiểm định khoảng tin cậy số ca
              mắc</h3>
            <apexchart type="line" height="250" :options="chart5Options" :series="chart5Series"></apexchart>
          </div>

          <div class="lg:col-span-5 bg-white p-5 rounded-xl shadow-sm border border-slate-200">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Phân nhóm năng lực tầm soát quốc
              gia</h3>
            <apexchart type="bubble" height="250" :options="chart6Options" :series="chart6Series"></apexchart>
          </div>
        </section>

      </div>
    </main>

  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import * as XLSX from 'xlsx';


import { use, registerMap } from 'echarts/core';
import { MapChart } from 'echarts/charts';
import {
  VisualMapComponent,
  TooltipComponent,
  GeoComponent,
} from 'echarts/components';
import { CanvasRenderer } from 'echarts/renderers';
import VChart from 'vue-echarts';

use([
  MapChart,
  VisualMapComponent,
  TooltipComponent,
  GeoComponent,
  CanvasRenderer,
]);

/* =========================================================
   BIẾN DỮ LIỆU
========================================================= */
const fullData = ref([]);
const years = ref([]);
const regions = ref([]);
const countries = ref([]);

const selectedYear = ref('');
const selectedRegion = ref('');
const selectedCountry = ref('');

const isMapReady = ref(false);

/* =========================================================
   HÀM CHUYỂN DỮ LIỆU VỀ SỐ

   Xử lý:
   - null
   - undefined
   - chuỗi rỗng
   - dấu phẩy trong số
   - ký hiệu $
   - khoảng trắng
========================================================= */
const toNumber = (value) => {
  if (
    value === null ||
    value === undefined ||
    value === ''
  ) {
    return null;
  }

  if (typeof value === 'number') {
    return Number.isFinite(value) ? value : null;
  }

  const cleanedValue = String(value)
    .replace(/\$/g, '')
    .replace(/,/g, '')
    .replace(/\s/g, '')
    .trim();

  if (
    cleanedValue === '' ||
    cleanedValue === '..' ||
    cleanedValue.toLowerCase() === 'n/a'
  ) {
    return null;
  }

  const number = Number(cleanedValue);

  return Number.isFinite(number) ? number : null;
};

/* =========================================================
   ĐỊNH DẠNG SỐ
========================================================= */
const formatNumber = (value) => {
  const number = toNumber(value);

  if (number === null) {
    return '0';
  }

  const absoluteNumber = Math.abs(number);

  if (absoluteNumber >= 1e12) {
    return `${(number / 1e12).toFixed(1)}T`;
  }

  if (absoluteNumber >= 1e9) {
    return `${(number / 1e9).toFixed(1)}B`;
  }

  if (absoluteNumber >= 1e6) {
    return `${(number / 1e6).toFixed(1)}M`;
  }

  if (absoluteNumber >= 1e3) {
    return `${(number / 1e3).toFixed(1)}K`;
  }

  return Math.round(number).toLocaleString('vi-VN');
};

/* =========================================================
   LỌC DỮ LIỆU THEO NĂM, KHU VỰC VÀ QUỐC GIA
========================================================= */
const filteredData = computed(() => {
  return fullData.value.filter((item) => {
    const matchYear =
      !selectedYear.value ||
      String(item.year) === String(selectedYear.value);

    const matchRegion =
      !selectedRegion.value ||
      item.g_whoregion === selectedRegion.value;

    const matchCountry =
      !selectedCountry.value ||
      item.country === selectedCountry.value;

    return matchYear && matchRegion && matchCountry;
  });
});

/* =========================================================
   KPI 1–4

   KPI 1: Tổng ca mắc
   KPI 2: Tỷ lệ tử vong trung bình
   KPI 3: Tỷ lệ mắc trung bình trên 100.000 dân
   KPI 4: Tổng thâm hụt tài chính
========================================================= */

const financeByRegion = computed(() => {
  const map = {};

  filteredData.value.forEach(item => {
    const region = item.g_whoregion;
    const gap = toNumber(item["finance.gap.tot"]);

    if (!region || gap === null) return;

    if (!map[region]) {
      map[region] = 0;
    }

    map[region] += gap;
  });

  return Object.entries(map)
    .map(([region, value]) => ({
      region,
      value,
    }))
    .sort((a, b) => b.value - a.value); // Sắp xếp giảm dần
});

const kpiTotals = computed(() => {
  let totalIncidence = 0;

  let totalCfr = 0;
  let cfrCount = 0;

  let totalIncidenceRate = 0;
  let incidenceRateCount = 0;

  let totalFinancialGap = 0;

  filteredData.value.forEach((item) => {
    const incidence = toNumber(item.e_inc_num);
    const cfr = toNumber(item.cfr_pct);
    const incidenceRate = toNumber(item.e_inc_100k);
    const financialGap = toNumber(item['finance.gap.tot']);

    if (incidence !== null) {
      totalIncidence += incidence;
    }

    if (cfr !== null) {
      totalCfr += cfr;
      cfrCount++;
    }

    if (incidenceRate !== null) {
      totalIncidenceRate += incidenceRate;
      incidenceRateCount++;
    }

    if (financialGap !== null) {
      totalFinancialGap += financialGap;
    }
  });

  return {
    incidence: totalIncidence,

    cfrAvg:
      cfrCount > 0
        ? (totalCfr / cfrCount).toFixed(2)
        : '0.00',

    inc100kAvg:
      incidenceRateCount > 0
        ? (totalIncidenceRate / incidenceRateCount).toFixed(2)
        : '0.00',

    financialGap: totalFinancialGap,
  };
});

/* =========================================================
   TỔNG HỢP DỮ LIỆU THEO NĂM

   Dùng cho:
   - Biểu đồ 2
   - Biểu đồ 4
   - Biểu đồ 5
========================================================= */
const yearlyData = computed(() => {
  const result = {};

  filteredData.value.forEach((item) => {
    const year = item.year;

    if (
      year === null ||
      year === undefined ||
      year === ''
    ) {
      return;
    }

    if (!result[year]) {
      result[year] = {
        year: Number(year),

        deathsTotal: 0,
        deathsHiv: 0,
        deathsExcludeHiv: 0,

        financialGap: 0,
        incidenceLow: 0,
        incidenceEstimate: 0,
        incidenceHigh: 0,
      };
    }

    const deathsTotal = toNumber(item.e_mort_num);
    const deathsHiv = toNumber(item.e_mort_tbhiv_num);
    const deathsExcludeHiv = toNumber(item.e_mort_exc_tbhiv_num);
    const financialGap = toNumber(item['finance.gap.tot']);
    const incidenceLow = toNumber(item.e_inc_num_lo);
    const incidenceEstimate = toNumber(item.e_inc_num);
    const incidenceHigh = toNumber(item.e_inc_num_hi);

    if (deathsTotal !== null) {
      result[year].deathsTotal += deathsTotal;
    }

    if (deathsHiv !== null) {
      result[year].deathsHiv += deathsHiv;
    }

    if (deathsExcludeHiv !== null) {
      result[year].deathsExcludeHiv += deathsExcludeHiv;
    }

    if (financialGap !== null) {
      result[year].financialGap += financialGap;
    }

    if (incidenceLow !== null) {
      result[year].incidenceLow += incidenceLow;
    }

    if (incidenceEstimate !== null) {
      result[year].incidenceEstimate += incidenceEstimate;
    }

    if (incidenceHigh !== null) {
      result[year].incidenceHigh += incidenceHigh;
    }
  });

  return Object.values(result).sort(
    (a, b) => a.year - b.year
  );
});

/* =========================================================
   BIỂU ĐỒ 1
   PHÂN BỐ DỊCH TỄ THEO e_inc_100k

   Nếu một quốc gia có nhiều năm:
   - Tính trung bình e_inc_100k
   - Khi chọn năm thì chỉ tính năm được chọn
========================================================= */
const mapData = computed(() => {
  const countriesMap = {};

  filteredData.value.forEach((item) => {
    const country = item.country;
    const incidenceRate = toNumber(item.e_inc_100k);

    if (!country || incidenceRate === null) {
      return;
    }

    if (!countriesMap[country]) {
      countriesMap[country] = {
        total: 0,
        count: 0,
      };
    }

    countriesMap[country].total += incidenceRate;
    countriesMap[country].count++;
  });

  return Object.entries(countriesMap).map(
    ([country, values]) => ({
      name: country,
      value:
        values.count > 0
          ? Number((values.total / values.count).toFixed(2))
          : 0,
    })
  );
});

const mapMaximum = computed(() => {
  const values = mapData.value
    .map((item) => item.value)
    .filter((value) => Number.isFinite(value));

  if (!values.length) {
    return 500;
  }

  return Math.max(...values);
});

const mapOptions = computed(() => {
  if (!isMapReady.value) {
    return {};
  }

  return {
    tooltip: {
      trigger: 'item',
      formatter: (params) => {
        const value =
          params.value === undefined ||
            params.value === null ||
            Number.isNaN(params.value)
            ? 'Không có dữ liệu'
            : `${Number(params.value).toFixed(2)} ca/100.000 dân`;

        return `
          <strong>${params.name}</strong>
          <br>
          Tỷ lệ mắc: ${value}
        `;
      },
    },

    visualMap: {
      min: 0,
      max: mapMaximum.value,
      left: 10,
      bottom: 10,
      text: ['Cao', 'Thấp'],
      calculable: true,
      realtime: false,
      inRange: {
        color: [
          '#edf8fb',
          '#b2e2e2',
          '#66c2a4',
          '#2ca25f',
          '#006d2c',
        ],
      },
    },

    series: [
      {
        name: 'Tỷ lệ mắc lao',
        type: 'map',
        map: 'world',
        roam: true,
        zoom: 1.05,
        data: mapData.value,

        emphasis: {
          label: {
            show: true,
          },
        },

        itemStyle: {
          borderColor: '#ffffff',
          borderWidth: 0.5,
        },

        nameMap: {
          Vietnam: 'Viet Nam',
          'United States': 'United States of America',
          Russia: 'Russian Federation',
          Laos: "Lao People's Democratic Republic",
          Syria: 'Syrian Arab Republic',
          Tanzania: 'United Republic of Tanzania',
          Bolivia: 'Bolivia (Plurinational State of)',
          Venezuela: 'Venezuela (Bolivarian Republic of)',
          Iran: 'Iran (Islamic Republic of)',
          Moldova: 'Republic of Moldova',
          'South Korea': 'Republic of Korea',
          'North Korea': "Democratic People's Republic of Korea",
          Brunei: 'Brunei Darussalam',
        },
      },
    ],
  };
});

/* =========================================================
   CẤU HÌNH CHUNG APEXCHART
========================================================= */
const commonChartOptions = {
  chart: {
    toolbar: {
      show: false,
    },
    animations: {
      enabled: true,
    },
    fontFamily: 'Arial, sans-serif',
  },

  dataLabels: {
    enabled: false,
  },

  grid: {
    borderColor: '#e2e8f0',
    strokeDashArray: 4,
  },

  noData: {
    text: 'Không có dữ liệu phù hợp',
    align: 'center',
    verticalAlign: 'middle',
  },
};

/* =========================================================
   BIỂU ĐỒ 2
   XU HƯỚNG TỬ VONG DO LAO
========================================================= */
const chart2Options = computed(() => ({
  ...commonChartOptions,

  chart: {
    ...commonChartOptions.chart,
    type: 'line',
  },

  stroke: {
    curve: 'smooth',
    width: [4, 3, 3],
    dashArray: [0, 5, 5],
  },

  markers: {
    size: [4, 3, 3],
  },

  colors: [
    '#2b6cb0',
    '#e53e3e',
    '#718096',
  ],

  xaxis: {
    categories: yearlyData.value.map((item) =>
      String(item.year)
    ),

    title: {
      text: 'Năm',
    },
  },

  yaxis: {
    title: {
      text: 'Số ca tử vong',
    },

    labels: {
      formatter: (value) => formatNumber(value),
    },
  },

  legend: {
    position: 'top',
    horizontalAlign: 'left',
  },

  tooltip: {
    shared: true,
    intersect: false,

    y: {
      formatter: (value) =>
        `${Number(value).toLocaleString('vi-VN')} ca`,
    },
  },
}));

const chart2Series = computed(() => [
  {
    name: 'Tổng tử vong do lao',
    data: yearlyData.value.map(
      (item) => item.deathsTotal
    ),
  },
  {
    name: 'Tử vong lao đồng nhiễm HIV',
    data: yearlyData.value.map(
      (item) => item.deathsHiv
    ),
  },
  {
    name: 'Tử vong lao không gồm HIV ',
    data: yearlyData.value.map(
      (item) => item.deathsExcludeHiv
    ),
  },
]);
/* =========================================================
   BIỂU ĐỒ 3
   TƯƠNG QUAN ĐỒNG NHIỄM HIV VÀ TỶ LỆ TỬ VONG
========================================================= */
const chart3Points = computed(() => {
  return filteredData.value
    .map((item) => {
      const hivRate = toNumber(item.e_tbhiv_prct);
      const cfrRate = toNumber(item.cfr_pct);

      if (hivRate === null || cfrRate === null) {
        return null;
      }

      return {
        x: hivRate,
        y: cfrRate,
        country: item.country || 'Không xác định',
        year: item.year || '',
      };
    })
    .filter(Boolean);
});

// BIỂU ĐỒ 3: Tương quan đồng nhiễm HIV và tỷ lệ tử vong
// Kích thước bong bóng đại diện cho dân số e_pop_num

const chart3Series = computed(() => {
  const points = filteredData.value
    .map((item) => {
      const hiv = toNumber(item.e_tbhiv_prct);
      const cfr = toNumber(item.cfr_pct);
      const population = toNumber(item.e_pop_num);

      if (
        hiv === null ||
        cfr === null ||
        population === null
      ) {
        return null;
      }

      return {
        x: hiv,
        y: cfr,

        // Chuẩn hóa dân số thành kích thước bong bóng
        // Giới hạn từ 4 đến 35 để bong bóng không quá lớn
        z: Math.max(
          4,
          Math.min(
            Math.sqrt(population / 1_000_000) * 4,
            35
          )
        ),

        country: item.country || 'Không xác định',
        year: item.year || '',
        population,
      };
    })
    .filter(Boolean);

  return [
    {
      name: 'Quốc gia',
      data: points,
    },
  ];
});

const chart3Options = computed(() => ({
  chart: {
    type: 'bubble',
    toolbar: {
      show: false,
    },
    zoom: {
      enabled: true,
      type: 'xy',
    },
  },

  colors: ['#805ad5'],

  dataLabels: {
    enabled: false,
  },

  fill: {
    opacity: 0.65,
  },

  stroke: {
    width: 1,
  },

  plotOptions: {
    bubble: {
      minBubbleRadius: 4,
      maxBubbleRadius: 35,
      zScaling: true,
    },
  },

  xaxis: {
    type: 'numeric',
    min: 0,

    title: {
      text: 'Tỷ lệ đồng nhiễm HIV (%)',
    },

    labels: {
      formatter: (value) =>
        Number(value).toFixed(1),
    },
  },

  yaxis: {
    min: 0,
    max: 100,

    title: {
      text: 'Tỷ lệ tử vong CFR (%)',
    },

    labels: {
      formatter: (value) =>
        Number(value).toFixed(1),
    },
  },

  grid: {
    borderColor: '#e2e8f0',
    strokeDashArray: 4,
  },

  legend: {
    show: false,
  },

  tooltip: {
    custom: ({ seriesIndex, dataPointIndex, w }) => {
      const point =
        w.config.series[seriesIndex].data[dataPointIndex];

      return `
        <div style="padding:10px; min-width:190px">
          <strong>${point.country}</strong>
          <br>
          Năm: ${point.year}
          <br>
          Đồng nhiễm HIV: ${point.x.toFixed(2)}%
          <br>
          CFR: ${point.y.toFixed(2)}%
          <br>
          Dân số: ${Number(point.population).toLocaleString('vi-VN')}
        </div>
      `;
    },
  },
}));

/* =========================================================
   BIỂU ĐỒ 4
   ĐÁNH GIÁ KHOẢNG TRỐNG TÀI CHÍNH
========================================================= */
const chart4Options = computed(() => ({
  chart: {
    type: "bar",
    toolbar: {
      show: false,
    },
  },

  colors: ["#4B5563"],

  plotOptions: {
    bar: {

      columnWidth: "55%",
      dataLabels: {
        position: "top"
      }
    }
  },

  dataLabels: {
    enabled: true,
    offsetY: -20,

    formatter: function (val) {
      return Number(val).toLocaleString("vi-VN");
    },

    style: {
      fontSize: "12px",
      fontWeight: "bold",
      colors: ["#374151"]
    }
  },

  xaxis: {
    categories: financeByRegion.value.map(i => i.region)
  },

  yaxis: {
    labels: {
      formatter: val => formatNumber(val)
    }
  }
}));
const chart4Series = computed(() => [
  {
    name: "finance.gap.tot (USD)",
    data: financeByRegion.value.map(i => i.value),
  },
]);

/* =========================================================
   BIỂU ĐỒ 5
   KIỂM ĐỊNH KHOẢNG TIN CẬY SỐ CA MẮC
========================================================= */
const chart5Options = computed(() => ({
  ...commonChartOptions,

  chart: {
    ...commonChartOptions.chart,
    type: 'line',
  },

  colors: [
    '#718096',
    '#3182ce',
    '#e53e3e',
  ],

  stroke: {
    curve: 'smooth',
    width: [2, 4, 2],
    dashArray: [6, 0, 6],
  },

  markers: {
    size: [2, 4, 2],
  },

  xaxis: {
    categories: yearlyData.value.map((item) =>
      String(item.year)
    ),

    title: {
      text: 'Năm',
    },
  },

  yaxis: {
    title: {
      text: 'Số ca mắc ước tính',
    },

    labels: {
      formatter: (value) => formatNumber(value),
    },
  },

  tooltip: {
    shared: true,
    intersect: false,

    y: {
      formatter: (value) =>
        `${Number(value).toLocaleString('vi-VN')} ca`,
    },
  },

  legend: {
    position: 'top',
  },
}));

const chart5Series = computed(() => [
  {
    name: 'Cận dưới',
    data: yearlyData.value.map(
      (item) => item.incidenceLow
    ),
  },
  {
    name: 'Ước tính trung tâm',
    data: yearlyData.value.map(
      (item) => item.incidenceEstimate
    ),
  },
  {
    name: 'Cận trên',
    data: yearlyData.value.map(
      (item) => item.incidenceHigh
    ),
  },
]);

/* =========================================================
   BIỂU ĐỒ 6
   PHÂN NHÓM NĂNG LỰC TẦM SOÁT QUỐC GIA

   Trục X: Tỷ lệ phát hiện ca bệnh c_cdr
   Trục Y: Tỷ lệ mắc e_inc_100k
   Kích thước: Dân số e_pop_num
========================================================= */
const screeningGroups = computed(() => {
  const groups = {
    low: [],
    medium: [],
    high: [],
  };

  filteredData.value.forEach((item) => {
    const cdr = toNumber(item.c_cdr);
    const incidenceRate = toNumber(item.e_inc_100k);
    const population = toNumber(item.e_pop_num);

    if (
      cdr === null ||
      incidenceRate === null ||
      population === null
    ) {
      return;
    }

    const bubble = {
      x: cdr,
      y: incidenceRate,
      z: Math.max(
        3,
        Math.min(population / 5000000, 30)
      ),
      country: item.country || 'Không xác định',
      year: item.year || '',
      population,
    };

    if (cdr < 70) {
      groups.low.push(bubble);
    } else if (cdr < 90) {
      groups.medium.push(bubble);
    } else {
      groups.high.push(bubble);
    }
  });

  return groups;
});

const chart6Options = computed(() => ({
  ...commonChartOptions,

  chart: {
    ...commonChartOptions.chart,
    type: 'bubble',
    zoom: {
      enabled: true,
      type: 'xy',
    },
  },

  colors: [
    '#e53e3e',
    '#d69e2e',
    '#38a169',
  ],

  xaxis: {
    type: 'numeric',
    min: 0,

    title: {
      text: 'Tỷ lệ phát hiện ca bệnh c_cdr (%)',
    },

    labels: {
      formatter: (value) => `${Number(value).toFixed(0)}%`,
    },
  },

  yaxis: {
    min: 0,

    title: {
      text: 'Tỷ lệ mắc lao/100.000 dân',
    },

    labels: {
      formatter: (value) => Number(value).toFixed(0),
    },
  },

  tooltip: {
    custom: ({ seriesIndex, dataPointIndex, w }) => {
      const point =
        w.config.series[seriesIndex].data[dataPointIndex];

      return `
        <div style="padding:10px">
          <strong>${point.country}</strong>
          <br>
          Năm: ${point.year}
          <br>
          CDR: ${point.x.toFixed(2)}%
          <br>
          Tỷ lệ mắc: ${point.y.toFixed(2)}/100.000 dân
          <br>
          Dân số: ${Number(point.population).toLocaleString('vi-VN')}
        </div>
      `;
    },
  },

  legend: {
    position: 'top',
  },
}));

const chart6Series = computed(() => [
  {
    name: 'Năng lực thấp: dưới 70%',
    data: screeningGroups.value.low,
  },
  {
    name: 'Năng lực trung bình: 70–89%',
    data: screeningGroups.value.medium,
  },
  {
    name: 'Năng lực cao: từ 90%',
    data: screeningGroups.value.high,
  },
]);

/* =========================================================
   ĐỌC FILE EXCEL VÀ TẢI BẢN ĐỒ
========================================================= */
onMounted(async () => {
  try {
    /* -----------------------------
       1. Tải GeoJSON bản đồ thế giới
    ----------------------------- */
    try {
      const mapResponse = await fetch(
        'https://raw.githubusercontent.com/apache/echarts/master/test/data/map/json/world.json'
      );

      if (!mapResponse.ok) {
        throw new Error(
          `Không tải được bản đồ: HTTP ${mapResponse.status}`
        );
      }

      const worldGeoJson = await mapResponse.json();

      /*
       * registerMap thuộc echarts/core.
       * Không dùng VChart.registerMap().
       */
      registerMap('world', worldGeoJson);

      isMapReady.value = true;
    } catch (mapError) {
      console.error(
        'Lỗi kết nối máy chủ nạp bản đồ nhiệt:',
        mapError
      );
    }

    /* -----------------------------
       2. Đọc file Excel
    ----------------------------- */
    const response = await fetch('/CT312_datasets.xlsx');

    if (!response.ok) {
      throw new Error(
        `Không tải được file Excel: HTTP ${response.status}`
      );
    }

    const arrayBuffer = await response.arrayBuffer();

    const workbook = XLSX.read(arrayBuffer, {
      type: 'array',
    });

    const worksheet = workbook.Sheets['data_full'];

    if (!worksheet) {
      throw new Error(
        'Không tìm thấy sheet "data_full" trong file Excel'
      );
    }

    const rawData = XLSX.utils.sheet_to_json(worksheet, {
      defval: null,
      raw: true,
    });

    /*
     * Xóa khoảng trắng ở đầu và cuối tên cột.
     * Ví dụ:
     * "finance.gap.tot " -> "finance.gap.tot"
     */
    const cleanedData = rawData.map((row) => {
      const cleanedRow = {};

      Object.entries(row).forEach(([key, value]) => {
        cleanedRow[String(key).trim()] = value;
      });

      return cleanedRow;
    });

    fullData.value = cleanedData;

    years.value = [
      ...new Set(
        cleanedData
          .map((item) => item.year)
          .filter(
            (value) =>
              value !== null &&
              value !== undefined &&
              value !== ''
          )
      ),
    ].sort((a, b) => Number(b) - Number(a));

    regions.value = [
      ...new Set(
        cleanedData
          .map((item) => item.g_whoregion)
          .filter(Boolean)
      ),
    ].sort();

    countries.value = [
      ...new Set(
        cleanedData
          .map((item) => item.country)
          .filter(Boolean)
      ),
    ].sort((a, b) => a.localeCompare(b));

    /*
     * Kiểm tra cột tài chính ngay trong phạm vi jsonData.
     */
    console.log(
      'Các cột trong dữ liệu:',
      Object.keys(cleanedData[0] || {})
    );

    console.log(
      'Có cột finance.gap.tot:',
      Object.prototype.hasOwnProperty.call(
        cleanedData[0] || {},
        'finance.gap.tot'
      )
    );

    console.log(
      '20 giá trị finance.gap.tot đầu tiên:',
      cleanedData
        .map((item) => item['finance.gap.tot'])
        .filter(
          (value) =>
            value !== null &&
            value !== undefined &&
            value !== ''
        )
        .slice(0, 20)
    );

    console.log(
      'Số dòng có dữ liệu finance.gap.tot:',
      cleanedData.filter(
        (item) =>
          toNumber(item['finance.gap.tot']) !== null
      ).length
    );
  } catch (error) {
    console.error(
      'Lỗi đồng bộ dữ liệu dịch tễ tổng quan:',
      error
    );
  }
});

const top10IncidenceData = computed(() => {
  const countryMap = {};

  filteredData.value.forEach((item) => {
    const country = item.country;
    const incidence = toNumber(item.e_inc_100k);

    if (!country || incidence === null) {
      return;
    }

    if (!countryMap[country]) {
      countryMap[country] = {
        total: 0,
        count: 0,
      };
    }

    countryMap[country].total += incidence;
    countryMap[country].count++;
  });

  return Object.entries(countryMap)
    .map(([country, values]) => ({
      country,

      // Chọn năm: chỉ có một giá trị của năm đó
      // Không chọn năm: lấy trung bình giai đoạn 2020–2024
      incidence: Number(
        (values.total / values.count).toFixed(2)
      ),
    }))
    .sort((a, b) => b.incidence - a.incidence)
    .slice(0, 10);
});
const top10IncidenceSeries = computed(() => [
  {
    name: "Tỷ lệ mắc",
    data: top10IncidenceData.value.map(
      (item) => item.incidence
    ),
  },
]);
const top10IncidenceOptions = computed(() => ({
  chart: {
    type: "bar",
    toolbar: {
      show: false,
    },
  },

  plotOptions: {
    bar: {
      horizontal: true,
      borderRadius: 4,
      barHeight: "65%",
      distributed: true,
      dataLabels: {
        position: "top",
      },
    },
  },

  dataLabels: {
    enabled: true,
    offsetX: -5,

    textAnchor: "end",

    style: {
      fontSize: "11px",
      fontWeight: "bold",
      colors: ["#ffffff"],
    },

    formatter(value) {
      return Number(value).toLocaleString("vi-VN", {
        minimumFractionDigits: 1,
        maximumFractionDigits: 1,
      });
    },
  },

  xaxis: {
    categories: top10IncidenceData.value.map(
      (item) => item.country
    ),

    title: {
      text: "Số ca mắc trên 100.000 dân",
    },

    labels: {
      formatter(value) {
        return Number(value).toLocaleString("vi-VN", {
          maximumFractionDigits: 0,
        });
      },
    },
  },

  yaxis: {
    labels: {
      maxWidth: 180,
    },
  },

  grid: {
    borderColor: "#e2e8f0",
    strokeDashArray: 4,
  },

  legend: {
    show: false,
  },

  tooltip: {
    y: {
      formatter(value) {
        return `${Number(value).toLocaleString("vi-VN", {
          maximumFractionDigits: 1,
        })} ca/100.000 dân`;
      },
    },
  },
}));
</script>