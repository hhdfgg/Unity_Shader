# Unity_Shader

- 全体の説明を簡単にした後に、サーフェースシェーダーについて分かったことを説明
 
- Unityで動くシェーダーはShaderLabという書式で書かれている

- ３Dオブジェクトはマテリアルによってシェーダーと紐付いているので、マテリアルにアタッチすることによって、自分で設定した色や質感が反映される
。

# シェーダーの種類
 - Standard Surface Shader(サーフェースシェーダー・標準シェーダー)
 - Vertex・Fragment / Unlit Shader(頂点・フラグメントシェーダ)
 - Image Effect Shader(イメージエフェクトシェーダー)
 - Compute Shader(コンピュートシェーダー)

##  Standard Surface Shader
 - 基本的な色や、ライトの影響、影、テクスチャの変更、サーフェスのプロパティー (アルべド、法線、エミッション、スペキュラーなど) を簡単に設定できる

## Vertex・Fragment / Unlit Shader
 - 基本的なライティング、光源からの影響をなしにしたシェーダー
 - 設定された色やテクスチャの色がそのまま表示される

  ### Vertex Shader
  - 頂点を動かして形状を変化させたりアニメーションさせたりすることが出来る
  - 頂点に色情報が設定されていれば、その色を変更したりできる
  - UI やエフェクトなどで使用

 ### Fragment Shader 
  - 光源や影の影響を考慮したり、色の変更、輪郭線の描画、ぼかしなどが出来る

## Image Effect Shade
 - カメラやテクスチャ画像に適用するシェーダー
 - ポストエフェクト (?)
 - 画面全体の色相変更、ぼかしなど
 - シーン遷移のときのエフェクト(フェードイン、フェードアウト、モザイク、ディストーション)が出来る

## Compute Shader
 - グラフィックスカード上で実行し、ゲームレンダリングの一部を加速させるためなどのシェーダー
 - 特定のプラットフォームで GPU を使うために利用
 - 最も高度で複雑なシェーダ

## 最後にあるShaderVariantCollection
 - シェーダーのロート・ビルド時間の最適化をするために使うらしい


# サーフェースシェーダーで今わかっていること
##  Properties
 - Propertiesにはインスペクタに公開する変数を書き初期化する
 - 自分でスライダーなどで値を作成することが出来る

```cs
	Properties {
		// インスペクターに表示させる項目の変数を設定している
		// プロパティ変数名 ("インスペクター表示名", 変数の型) = 初期値

		// インスペクターのデフォルトの色が白になる // 型名がColorなのでカラーパレットが出てくる
		_Color ("Color", Color) = (1,1,1,1) // (R, G, B, A) (0～1の間) 

		// テクスチャが割り当てられていないときは、白になるよう初期化
		_MainTex ("Albedo (RGB)", 2D) = "white" {}

		// 型名Range(min, max)でスライダーで設定できるようになる
		_Glossiness ("Smoothness", Range(0,1)) = 0.5
		_Metallic ("Metallic", Range(0,1)) = 0.0

		// 例として、アルファ値をスライダーで設定できるようにインスペクターに表示させたいなら
		_Alpha ("Alpha_Value",Range(0,1)) = 1
		//と書くとインスペクターに表示される
	}
```

 ## SubShader
 - ライティングや透明度などのシェーダの設定を書く
 
```cs
// 描画種類を設定できる?
Tags { "RenderType"="Opaque" }

// 不透明
Tags {
"Queue" = "Geometry"
"RenderType" = "Opaque"
}

// 透明
Tags {
    "Queue" = "Transparent"
    "RenderType" = "Transparent"
}

// アルファテスト (葉っぱなど)
Tags {
"Queue" = "AlphaTest"
"RenderType" = "TransparentCutout"
}
```
## void surf (Input IN, inout SurfaceOutputStandard o)
- surfではどこにどの値を入れるかを設定

```cs
		void surf (Input IN, inout SurfaceOutputStandard o) {
			// fixed4型(低精度)の変数cに代入し、Inspectorでテクスチャの明度や色を調整
			fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
			// アルベドにRGBを代入代入
			o.Albedo = c.rgb;
			
			// スライダーで変えた値をオブジェクトのMetallicとSmoothnessに代入
			o.Metallic = _Metallic;
			o.Smoothness = _Glossiness;
			// アルファチャンネルの値を代入する
			o.Alpha = c.a;
		}
```

[両面描画の解説](Both Sides Drawing.md)
