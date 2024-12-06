```
using UnityEngine;

public class DrawLightmappedMeshInstanced : MonoBehaviour
{
    public Material mat;
    public Mesh mesh;
    public Matrix4x4[] instData;
    public Vector4[] lightmapOffset;
    public MaterialPropertyBlock block;

    void Start()
    {
        Renderer[] renderers = GetComponentsInChildren<Renderer>();
        mat = new Material(renderers[0].sharedMaterials[0]);
        mat.EnableKeyword("LIGHTMAP_ON");
        mat.EnableKeyword("DIRLIGHTMAP_COMBINED");

        mesh = renderers[0].GetComponent<MeshFilter>().mesh;

        instData = new Matrix4x4[renderers.Length];
        lightmapOffset = new Vector4[renderers.Length];



        for (int i = 0; i < renderers.Length; i++)
        {
            renderers[i].enabled = false;
            instData[i] = Matrix4x4.TRS(renderers[i].transform.position, renderers[i].transform.rotation, renderers[i].transform.lossyScale);
            lightmapOffset[i] = renderers[i].lightmapScaleOffset;
        }

        block = new MaterialPropertyBlock();
        block.SetVectorArray("custom_lightST", lightmapOffset);
        block.SetTexture("unity_Lightmap", LightmapSettings.lightmaps[renderers[0].lightmapIndex].lightmapColor);
        if (LightmapSettings.lightmaps[renderers[0].lightmapIndex].lightmapDir != null)
        {
            block.SetTexture("unity_LightmapInd", LightmapSettings.lightmaps[renderers[0].lightmapIndex].lightmapDir);
        }
        if (LightmapSettings.lightmaps[renderers[0].lightmapIndex].shadowMask != null)
        {
            block.SetTexture("unity_ShadowMask", LightmapSettings.lightmaps[renderers[0].lightmapIndex].shadowMask);
        }
    }


    void Update()
    {
        Graphics.DrawMeshInstanced(mesh, 0, mat, instData, instData.Length, block, UnityEngine.Rendering.ShadowCastingMode.Off);
    }


}


```
