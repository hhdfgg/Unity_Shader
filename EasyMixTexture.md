# 簡単に２枚のテクスチャをブレンドする方法
簡単なシェーダーを書いて、２枚のテクスチャをブレンドする方法を紹介します。

## 準備
 1. ヒエラルキービューで"create"->"3DObject"->"Quad"を生成、名前は"TexTest"としておきます。
 1. プロジェクトビューで"create"->"Material"を生成、名前は"TexTest"
 1. プロジェクトビューで"create"->"Shader"->"StanderdSurfaceShader"を生成、名前は"TexTest"
 1. マテリアルにシェーダーを割り当てたあとに、Quad(Textest)にマテリアルを割り当てる
 ※割り当てる = ドラック&ドロップ
 1. ２枚以上の異なるテクスチャをUnityにインポート
 
 以上準備は終了
 
## シェーダーを書く
 ### Properties

```cs
  // メインテクスチャの変数の型とインスペクター上の名前と初期値を設定
  _MainTex ("MainTex", 2D) = "white" {}
  // ２枚目のテクスチャの変数の型とインスペクター上の名前と初期値を設定
  _Tex2("Tex2",2D)  = "white"{}
```

 ### SubShaderの"#pragma target 3.0"の下に
 
 ```cs
    // プロパティで受け取ったデータをシェーダ内で使うための定義
    // テクスチャはsampler2D型
    // sampler2D型の_MainTexと_Tex2を定義する
  	sampler2D _MainTex;
  	sampler2D _Tex2;
 ```
 
 ### void surf
 
 ```cs
 		void surf (Input IN, inout SurfaceOutputStandard o) {
      // tex2D関数:tex2D (サンプラー ステート, テクスチャー座標);
      // UV座標（uv_MainTex）からテクスチャ（_MainTex）上のピクセルの色を計算して返す関数
      // c1がMainTex、c2がTex2
			fixed4 c1 = tex2D (_MainTex, IN.uv_MainTex);
			fixed4 c2 = tex2D(_Tex2, IN.uv_MainTex);
      // アルベドにc1のRGBとc2のRGBを掛ける
      // "+" "-" "*" "/" に変えてみて自分が好きな色に変えることが出来る
			o.Albedo = c1.rgb * c2.rgb;
		}
 ```
 
 
## 最後にプロジェクトビューで
 - TexTest(Material)のインスペクターにテクスチャを割り当ててみる
 - c1.rgb * c2.rgbの * を"+" "-" "/" に変えてみる
 
 ### スクリプト全体
 
 ```cs
 Shader "Custom/Textest" {
	Properties {
		_MainTex ("MainTex", 2D) = "white" {}
		_Tex2("Tex2",2D)  = "white"{}
	}
	SubShader {
		Tags { "RenderType"="Opaque" }
		LOD 200

		CGPROGRAM

		#pragma surface surf Standard fullforwardshadows

		#pragma target 3.0

		// プロパティで受け取ったデータをシェーダ内で使うための定義
		// テクスチャはsampler2D型
		// sampler2D型の_MainTexと_Tex2を定義する
		sampler2D _MainTex;
		sampler2D _Tex2;

		struct Input {
			float2 uv_MainTex;
		};

		UNITY_INSTANCING_BUFFER_START(Props)

		UNITY_INSTANCING_BUFFER_END(Props)

		void surf (Input IN, inout SurfaceOutputStandard o) {
			// tex2D関数:tex2D (サンプラー ステート, テクスチャー座標);
			// UV座標（uv_MainTex）からテクスチャ（_MainTex）上のピクセルの色を計算して返す関数
			// c1がMainTex、c2がTex2
			fixed4 c1 = tex2D (_MainTex, IN.uv_MainTex);
			fixed4 c2 = tex2D(_Tex2, IN.uv_MainTex);
			// アルベドにc1のRGBとc2のRGBを掛ける
			// "+" "-" "*" "/" に変えてみて自分が好きな色に変えることが出来る
			o.Albedo = c1.rgb * c2.rgb;
		}
		ENDCG
	}
	FallBack "Diffuse"
}

 ```
 
 
## 応用

### ベースカラーを設定してしてみる
- カラーパレットでMainTexのベースカラーを設定できるようにする

### Properties

```cs
		_Color("Color", Color) = (1,1,1,1)
		_MainTex ("MainTex", 2D) = "white" {}
		_Tex2("Tex2",2D)  = "white"{}
```


 ### SubShaderの"#pragma target 3.0"の下に
 
 ```cs
 		sampler2D _MainTex;
		sampler2D _Tex2;
		fixed4 _Color;
 ```
 

  ### void surf
  
  ```cs
  		fixed4 c1 = tex2D (_MainTex, IN.uv_MainTex) * _Color;
			fixed4 c2 = tex2D(_Tex2, IN.uv_MainTex);
			o.Albedo = c1.rgb * c2.rgb;
  ```
  
  
  ### スクリプト全体
  
  ```cs
  Shader "Custom/Textest" {
	Properties {
		_Color("Color", Color) = (1,1,1,1)
		_MainTex ("MainTex", 2D) = "white" {}
		_Tex2("Tex2",2D)  = "white"{}
	}
	SubShader {
		Tags { "RenderType"="Opaque" }
		LOD 200

		CGPROGRAM

		#pragma surface surf Standard fullforwardshadows

		#pragma target 3.0

		// プロパティで受け取ったデータをシェーダ内で使うための定義
		// テクスチャはsampler2D型
		// sampler2D型の_MainTexと_Tex2を定義する
		sampler2D _MainTex;
		sampler2D _Tex2;
		fixed4 _Color;

		struct Input {
			float2 uv_MainTex;
		};

		UNITY_INSTANCING_BUFFER_START(Props)

		UNITY_INSTANCING_BUFFER_END(Props)

		void surf (Input IN, inout SurfaceOutputStandard o) {
			// tex2D関数:tex2D (サンプラー ステート, テクスチャー座標);
			// UV座標（uv_MainTex）からテクスチャ（_MainTex）上のピクセルの色を計算して返す関数
			// c1がMainTex、c2がTex2
			fixed4 c1 = tex2D (_MainTex, IN.uv_MainTex) * _Color;
			fixed4 c2 = tex2D(_Tex2, IN.uv_MainTex);
			// アルベドにc1のRGBとc2のRGBを掛ける
			// "+" "-" "*" "/" に変えてみて自分が好きな色に変えることが出来る
			o.Albedo = c1.rgb * c2.rgb;
		}
		ENDCG
	}
	FallBack "Diffuse"
}

  ```
