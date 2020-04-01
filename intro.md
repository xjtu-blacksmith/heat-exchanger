---
layout: page
title: 计算实验
---

# Chen's correlation

<div class='col-md-6'>
<label for='x'>干度 \(x\)</label>
<input type='text' class='form-control' id='x'><br/>


<label for='mu_l'>液体粘度 \(\mu_L\)</label>
<div class='input-group'>
    <input type='text' class='form-control' id='mu_l'>
    <span class='input-group-addon'>Pa.s</span>
</div>


<br/>

<button class='btn btn-primary' id='calc' type='button'>计算</button>
</div>

<div id='result'></div>

<script>
$('#calc').on('click', function() {
    const parser = math.parser()
    let scope = {
        x : 0.5,  // 干度
        mu_l : 199e-6,  // 液相粘度
        mu_g : 12e-6,  // 气相粘度
        rho_l : 1265,  // 液相密度
        rho_g : 25,  // 气相密度
        G: 200,  // 质量流量
        i_fg: 200,  // 潜热 
        d_i: 0.013,  // 内径
        Pr_l: 2.51,  // Prandtl number
        P_r: 0.1168,  // reduced pressure
        q: 10e3,  // 热流密度
        M: 86.47,  // 相对分子质量
        m: 0.12,  // 光滑管指数
    }
    scope.x = Number($('#x').val())
    scope.mu_l = Number($('#mu_l').val())
    parser.scope = scope  // pass scope to parser
    parser.evaluate('Re_l = G * d_i * (1-x) / mu_l')
    parser.evaluate('Xtt = ((1-x)/x)^0.875 * (mu_l/mu_g)^0.125 * (rho_g/rho_l)^0.5')
    parser.evaluate('h_CV = 0.023 * Re_l^0.8 * Pr_l^0.4')
    parser.evaluate('h_NB = 55 * q^0.67 * M^(-0.5) * P_r^m * (-log10(P_r))^(-0.55)')
    parser.evaluate('E = 2.35/(1/Xtt + 0.213)^0.736')
    parser.evaluate('Re = Re_l * E^1.25')
    parser.evaluate('S = 1/(1 + 2.53e-6 * Re^1.17)')
    parser.evaluate('h = S * h_NB + E * h_CV')
    const h = parser.get('h')
    $('#result').append('<p>表面换热系数 h：' + h.toString() + 'W/m^2</p>')
})
</script>