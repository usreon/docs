폴더를 돌아서 파일의 이름을 엑셀에 있는 CODE와 맞춰주기 위해서

1. 파일명 slice

파일명과 CODE가 맞으면 그 로우에 있는 2. x, y 좌표를 채운다

파일 쓰기가 끝나면 엑셀을 읽어서
전체 데이터를 불러온 뒤 3. post processing을 진행한다.

```ts
  @Post('nia/sensoryMachine')
  @ApiConsumes('multipart/form-data')
  @ApiOperation({ summary: 'Read Excel for NIA' })
  @UseInterceptors(FileInterceptor('file'))
  async postProcessingForNia(
    @Res() res: Response,
    @Body() params: SubtaskExcelFileDto,
    @UploadedFile() file: Express.Multer.File,
  ) {
    const { workbook } =
      await this.scriptsService.writeValueOnExcelFromJsonfileForNia(
        params,
        file,
      );
    const fileName = 'nia_result.xlsx';

    res.setHeader(
      'Content-Type',
      'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;',
    );
    res.setHeader('Content-Disposition', `attachment; filename=${fileName}`);

    await workbook.xlsx.write(res);
    res.end();
  }
```

```ts
public async writeValueOnExcelFromJsonfileForNia(
    body: SubtaskExcelFileDto,
    file: Express.Multer.File,
  ) {
    const { subtaskId } = body;
    // json
    const clientName = 'nia';
    const rootPath = `./scripts/data/nia`;
    const sourcePath = `${rootPath}/${clientName}-s${subtaskId}-results`;

    // excel
    const workbook = await this.excelService.readBufferType(file.buffer);
    const worksheet = workbook.getWorksheet('SC_ABR(0907)_final');

    const files = readdirSync(sourcePath);

    for await (const fileName of files) {
      if (!fileName.endsWith('.json')) {
        console.log('json 파일이 아닙니다.', fileName);
        break;
      }

      //eslint-disable-next-line
      let { labels, images, name } = readJSONSync(`${sourcePath}/${fileName}`);
      const idx = name.indexOf('.');
      name = name.slice(0, idx);

      labels?.forEach((eachLabel: any) => {
        const { label, boundingPoly, ...rest } = eachLabel;
        const { type, vertices } = boundingPoly;
        const x = vertices[0].x;
        const y = vertices[0].y;

        worksheet.eachRow({ includeEmpty: true }, (row, rowNum) => {
          if (rowNum > 2) {
            const fileName = row.getCell(1).value;
            if (fileName === name) {
              if (label === '5') {
                row.getCell(27).value = x;
                row.getCell(28).value = y;
                row.commit();
              }
              if (label === '10') {
                row.getCell(30).value = x;
                row.getCell(31).value = y;
                row.commit();
              }
              if (label === '15') {
                row.getCell(33).value = x;
                row.getCell(34).value = y;
                row.commit();
              }
              if (label === '20') {
                row.getCell(36).value = x;
                row.getCell(37).value = y;
                row.commit();
              }
              if (label === '25') {
                row.getCell(39).value = x;
                row.getCell(40).value = y;
                row.commit();
              }
              if (label === '30') {
                row.getCell(42).value = x;
                row.getCell(43).value = y;
                row.commit();
              }
              if (label === '35') {
                row.getCell(45).value = x;
                row.getCell(46).value = y;
                row.commit();
              }
              if (label === '40') {
                row.getCell(48).value = x;
                row.getCell(49).value = y;
                row.commit();
              }
              if (label === '45') {
                row.getCell(51).value = x;
                row.getCell(52).value = y;
                row.commit();
              }
              if (label === '50') {
                row.getCell(54).value = x;
                row.getCell(55).value = y;
                row.commit();
              }
              if (label === '55') {
                row.getCell(57).value = x;
                row.getCell(58).value = y;
                row.commit();
              }
              if (label === '60') {
                row.getCell(60).value = x;
                row.getCell(61).value = y;
                row.commit();
              }
              if (label === '65') {
                row.getCell(63).value = x;
                row.getCell(64).value = y;
                row.commit();
              }
              if (label === '70') {
                row.getCell(66).value = x;
                row.getCell(67).value = y;
                row.commit();
              }
              if (label === '75') {
                row.getCell(69).value = x;
                row.getCell(70).value = y;
                row.commit();
              }
              if (label === '80') {
                row.getCell(72).value = x;
                row.getCell(73).value = y;
                row.commit();
              }
              if (label === '85') {
                row.getCell(75).value = x;
                row.getCell(76).value = y;
                row.commit();
              }
              if (label === '90') {
                row.getCell(78).value = x;
                row.getCell(79).value = y;
                row.commit();
              }
              if (label === '100') {
                row.getCell(81).value = x;
                row.getCell(82).value = y;
                row.commit();
              }
              if (label === '105') {
                row.getCell(84).value = x;
                row.getCell(85).value = y;
                row.commit();
              }
            }
          }
        });
      });
    }

    return {
      workbook,
    };
  }
```

```ts
import { mkdir, writeFileSync } from "fs-extra";
import readXlsxFile from "read-excel-file/node";

const clientName = "nia";
const rootPath = `./scripts/data/${clientName}`;
const excelFilePath = `${rootPath}/nia_result.xlsx`;

const sourcePath = `${rootPath}/${clientName}-results`;
const targetPath = `${sourcePath}-output`;

let num = 0;

const main = async () => {
    try {
        mkdir(targetPath);
    } catch (e) {}

    const rows = await readXlsxFile(excelFilePath);
    for (const row of rows) {
        // 헤더 제외
        if (row[0] === "CODE" || row[0] === null) {
            num++;
            continue;
        }

        const code = row[0]; // file name
        const hospital = row[1];
        const age = row[2];
        const sex = row[3];
        const hearingLoss = row[4];
        const tinnitus = row[5];
        const dizziness = row[6];
        const earFullNess = row[7];
        const dm = row[8];
        const htn = row[9];
        const sa = row[22];
        const tinnitogram = {
            loudNess: row[23],
            pitch: row[24],
        };
        const hearingImpairment = row[85];
        const measurements = row[86];
        const tumorSize = row[87];

        const pta = [
            {
                ac: {
                    250: row[10],
                    500: row[11],
                    1000: row[12],
                    2000: row[13],
                    4000: row[14],
                    8000: row[15],
                },
                bc: {
                    250: row[16],
                    500: row[17],
                    1000: row[18],
                    2000: row[19],
                    4000: row[20],
                    8000: row[21],
                },
            },
        ];

        const annotations = { name: "abr", type: "keypoint" };
        const vLattncy = [
            { "5db": row[25] },
            { "10db": row[28] },
            { "15db": row[31] },
            { "20db": row[34] },
            { "25db": row[37] },
            { "30db": row[40] },
            { "35db": row[43] },
            { "40db": row[46] },
            { "45db": row[49] },
            { "50db": row[52] },
            { "55db": row[55] },
            { "60db": row[58] },
            { "65db": row[61] },
            { "70db": row[64] },
            { "75db": row[67] },
            { "80db": row[70] },
            { "85db": row[73] },
            { "90db": row[76] },
            { "100db": row[79] },
            { "105db": row[82] },
        ];
        const vLattncyKeyPoint = {
            "5db": [{ x: row[26], y: row[27] }],
            "10db": [{ x: row[29], y: row[30] }],
            "15db": [{ x: row[32], y: row[33] }],
            "20db": [{ x: row[35], y: row[36] }],
            "25db": [{ x: row[38], y: row[39] }],
            "30db": [{ x: row[41], y: row[42] }],
            "35db": [{ x: row[44], y: row[45] }],
            "40db": [{ x: row[47], y: row[48] }],
            "45db": [{ x: row[50], y: row[51] }],
            "50db": [{ x: row[53], y: row[54] }],
            "55db": [{ x: row[56], y: row[57] }],
            "60db": [{ x: row[59], y: row[60] }],
            "65db": [{ x: row[62], y: row[63] }],
            "70db": [{ x: row[65], y: row[66] }],
            "75db": [{ x: row[68], y: row[69] }],
            "80db": [{ x: row[71], y: row[72] }],
            "85db": [{ x: row[74], y: row[75] }],
            "90db": [{ x: row[77], y: row[78] }],
            "100db": [{ x: row[80], y: row[81] }],
            "105db": [{ x: row[83], y: row[84] }],
        };

        const labels = [
            {
                code,
                hospital,
                age,
                sex,
                hearingLoss,
                tinnitus,
                dizziness,
                earFullNess,
                dm,
                htn,
                sa,
                ...tinnitogram,
                pta: pta,
                vLattncy: vLattncy,
                vLattncyKeyPoint: vLattncyKeyPoint,
                annotations: annotations,
                hearingImpairment,
                measurements,
                tumorSize,
            },
        ];

        const outputPath = `${targetPath}/${code}.json`;
        writeFileSync(outputPath, JSON.stringify(labels, null, 4));
    }
    console.log(num);
};

main()
    .then(() => {
        process.exit(0);
    })
    .catch((error) => console.error(error));
```

```ts
  public async extractContructionSand() {
    const SHEET_NAME = '한글';
    const excelPath = path.join(
      __dirname,
      `../../../scripts/data/sendfile_13box_final.xlsx`,
    );

    const clientName = 'nia';
    const subtaskIds = [490, 531, 538, 544];
    type excelHeaders = {
      number: string;
      year: string;
      tag: string;
      sampleNumber: string;
      aggregateSource: string;
      longitude: string;
      latitude: string;
      city1: string;
      city2: string;
      addr1: string;
      addr2: string;
      particleAmt: string;
      surfaceDryingDensity: string;
      absoluteDryingDensity: string;
      trueDensity: string;
      absorptionRate: string;
      assemblyRate: string;
      stability: string;
    };

    const map = {
      번호: 'number',
      년도: 'year',
      태그값: 'tag',
      시료번호: 'sampleNumber',
      골재원: 'aggregateSource',
      경도: 'longitude',
      위도: 'latitude',
      시도: 'city1',
      시군: 'city2',
      읍면동: 'addr1',
      리: 'addr2',
      잔입자통과량: 'particleAmt',
      표면건조밀도: 'surfaceDryingDensity',
      절대건조밀도: 'absoluteDryingDensity',
      진밀도: 'trueDensity',
      흡수율: 'absorptionRate',
      조립률: 'assemblyRate',
      안정성: 'Stability',
    };

    try {
      const { rows } = await readXlsxFile<excelHeaders>(excelPath, {
        sheet: SHEET_NAME,
        map,
      });

      subtaskIds.map(async (subtaskId) => {
        console.log(subtaskId, 'started');

        const sourcePath = path.join(
          __dirname,
          `../../../scripts/data/${clientName}-s${subtaskId}-results`,
        );
        const targetPath = `${sourcePath}-output`;

        try {
          mkdir(targetPath);
        } catch (e) {
          console.error(e);
        }

        const files = readdirSync(sourcePath);

        await Promise.all(
          files.map(async (fileName) => {
            if (!fileName.endsWith('.json')) {
              return;
            }

            const jsonData = readJSONSync(`${sourcePath}/${fileName}`);
            const name = jsonData.name as string;
            const namePrefix = name.substring(0, 3);

            const labels = jsonData.labels as any[];
            const shapes = labels.map((label) => ({
              name: label.boundingPoly.type,
              type: label.label,
              minDotCount: 0,
              maxDotCount: 100,
              vertices: label.boundingPoly.vertices,
            }));

            rows.map((row) => {
              const sampleNumber = row.sampleNumber;

              console.log(sampleNumber);

              if (sampleNumber == namePrefix) {
                const address1 = `${row.latitude}, ${row.longitude}`;
                const address2 = `${row.city1} ${row.city2} ${row.addr1} ${row.addr2}`;

                const obj = {
                  id: name,
                  image: {
                    name,
                    size: jsonData.labels[0].imgSize,
                  },
                  dataset: {
                    type: 1,
                    number: row.number,
                    year: row.year,
                    address1,
                    address2,
                    sort: row.aggregateSource,
                    camera: '',
                  },
                  shapes,
                };

                const idx = fileName.indexOf('-') + 1;
                const fileNameArr = fileName.substring(idx).split('.');
                const imageName = fileNameArr[0];
                const resultFileName = `${sampleNumber}_${row.year}_${address2}_${imageName}.json`;
                const outputPath = `${targetPath}/${resultFileName}`;

                writeFileSync(outputPath, JSON.stringify(obj, null, 4));
              }
            });
          }),
        );
      });
    } catch (error) {
      console.error(error);
    }
  }
```
