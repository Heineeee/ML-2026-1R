# ML-2026-1R
ICU

##################전처리############### 
0. Load metadata / labels
   - True / False만 사용
   - Reject / Uncertain 제거

1. Patient-record-level train/val/test split
   - event-level split 금지
   - 같은 patient record가 train/test에 같이 들어가면 leakage

2. Load WFDB signal

3. Check or resample to 250 Hz

4. Channel mapping
   - ECG1, ECG2, PPG, ABP
   - missing channel이면 mask=0

5. Cut window around alarm onset
   - main real-time setting: [-10s, 0s]
   - index 기준: [72500, 75000]
   - output length: T = 2500

6. Signal filtering
   ECG:
   - high-pass 1 Hz
   - 2nd-order Butterworth low-pass 30 Hz
   - notch filter

   PPG:
   - high-pass: stopband 0.3 Hz / passband 0.5 Hz
   - low-pass: passband 5 Hz / stopband 8 Hz

   ABP:
   - 논문 본문에는 ABP filter detail이 명시적으로 없음
   - 그래서 최소 구현은 notch 정도만 하거나 raw+normalization으로 두고,
     README에 명시하는 게 안전함

7. Segment-wise z-normalization
   - 각 sample, 각 available channel별로 mean/std 계산
   - missing channel에는 적용하지 않음

8. Missing channel → zero imputation
   - missing channel waveform = 0
   - m_channel에 0 표시

9. Save .pt
   - X: [N, 4, 2500]
   - y: [N]
   - m_channel: [N, 4]
   - names / record_ids
   - channels = ["ECG1", "ECG2", "PPG", "ABP"]
   - sampling_freq = 250
   - crop_samples = [72500, 75000]
##################전처리############### 
